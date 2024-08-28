# Mash-Off: Monster Rumble
![image](https://github.com/Pirat1001/Portfolio/blob/main/Images/mash-off-monsterrumble_logo.png)  

MASH-OFF: Monster Rumble is a local multiplayer twinstick party game about knocking your opponents out of an arena using various cartoony weapons while they try to do the same to you.  

[Itch.io](https://yrgo-game-creator.itch.io/mash-off-monster-rumble)  
[Github Repository](https://github.com/LadyRonja/ArenaEject)  

# Contribution
## Physics
I've worked on the player movement and its rotation.  

Implemented jumping for the player.  

Adjusted the gravity in order to work as desired.  

Worked with debugging/testing.  

The knockback handler, how players interact when being hit by different objects.  

<details>
  <summary>Code I wrote for jumping</summary>

  ```csharp
      using UnityEngine;
      using UnityEngine.InputSystem;
      
      [RequireComponent(typeof(GroundChecker))]
      [RequireComponent(typeof(Rigidbody))]
      public class Jump : MonoBehaviour
      {
          [HideInInspector] public bool appropriatlySpawned = false;
          private Rigidbody rb;
          private GroundChecker groundChecker; // TODO: Make independant
      
          [SerializeField] private float jumpForce = 6f;
          [SerializeField] private float jumpAmount = 1;
          public bool CanJump { get => GetCanJump();  }
          public bool CanMultiJump { get => GetCanMultiJump(); }
          public bool CanCoyoteJump { get => GetCanCoyoteJump(); }
          private float jumpCounter = 0;
      
          private void Awake()
          {
              rb = GetComponent<Rigidbody>();
              groundChecker = GetComponent<GroundChecker>();
          }
      
          private void Update()
          {
              JumpCounterResetCheck();
          }
      
          private bool GetCanJump()
          {
              // Check if the player is on the ground or if they've reached the maximum jumps
              if (groundChecker.IsGrounded)
              {
                  return true;
              }
              else
              {
                  return false; 
              }
          }
      
          private bool GetCanMultiJump()
          {
              if (groundChecker.IsGrounded) { return false; }
              if (jumpAmount < 2) { return false; }
              if (jumpCounter < jumpAmount) { return false; }
              return true;
          }
      
          private bool GetCanCoyoteJump()
          {
              if (jumpCounter != 0) { return false; }
              return true;
          }
      
          private void OnLeftTrigger(InputValue value)
          {
              float input = value.Get<float>();
              if(input > 0.5) { 
                  OnSouthButtonDown(value);
              }
          }
      
          private void OnLeftBumperDown(InputValue value)
          {
              OnSouthButtonDown(value);
          }
      
          private void OnSouthButtonDown(InputValue value)
          {
              if (StaticStats.gameOver) return;
              if (CanJump)
              {
                  AttemptJump();
              }
              else if (CanMultiJump)
              {
                  AttemptDoubleJump();
              }
              else if(CanCoyoteJump)
              {
                  ExecuteJump((Vector3.up + transform.forward).normalized);
              }
          }
      
          private void AttemptJump()
          {
              if (!groundChecker.IsGrounded)
                  return;
      
              ExecuteJump(Vector3.up);
          }
      
          private void AttemptDoubleJump()
          {
              if (groundChecker.IsGrounded)
                  return;
      
              Vector3 jumpDirection = (Vector3.up + rb.velocity.normalized).normalized;
      
              ExecuteJump(jumpDirection);
          }
      
          private void ExecuteJump(Vector3 jumpDirection)
          {
              // Increment jump counter
              jumpCounter++;
      
              // Perform jump
              rb.velocity = jumpDirection * jumpForce;
          }
      
          private void JumpCounterResetCheck()
          {
              if (jumpCounter == 0) { return; }
              if (rb.velocity.y > 0) { return; }
      
              if (groundChecker.IsGrounded) { 
                  jumpCounter = 0; 
              }
          }
      }
```
</details>

## Weapons
Adjusted each weapon physics in order for it to work as desired when dropped.  

Created the functionality for throwing the weapon and the weapon throwing mid-jump.  

Created all weapon projectiles and how they interact with the surroundings and other players.  

Made the weapon holding point for each weapon so it doesn't look weird while holding it.  

The explosion physics and functionality.  

Created the unusual behaviour for the rocket that's launched by the rocket launcher.  

<details>
  <summary>Mine code that I wrote</summary>

  ```csharp
  using UnityEngine;

  public class Grenade : Ammo
  {
      [SerializeField] protected bool explodeOnImpact = false;
      [SerializeField] protected float explodeTime = 1.5f;
      [SerializeField] protected float explosionDelay = 10f;
      [SerializeField] protected float proximityDistance = 6f;
      [SerializeField] protected GameObject explosionPrefab;
      public Collider triggerCollider;
      public LayerMask freezingGroundLayer;
  
      protected override void Start()
      {
          rb = GetComponent<Rigidbody>();
          rb.velocity = moveDir * moveSpeed;
      }
  
      private void Update()
      {
          if (rb.isKinematic)
          {
              Collider[] colliders = Physics.OverlapSphere(transform.position, proximityDistance);
              foreach (Collider collider in colliders)
              {
                  if (collider.gameObject.CompareTag("Player"))
                  {
                      float distance = Vector3.Distance(transform.position, collider.transform.position);
                      if (distance <= proximityDistance)
                      {
                          Explode();
                          break; 
                      }
                  }
              }
          }
      }
  
      private void OnCollisionEnter(Collision other)
      {
          if (other.gameObject.TryGetComponent<KnockBackHandler>(out KnockBackHandler hit))
          {   
              // apply knockback if moving fast enough
              if (rb.velocity.sqrMagnitude > 2f)
              {
                  Vector3 dir = rb.velocity.normalized;
                  dir.y = 0;
                  hit.GetKnockedBack(dir, knockbackForce, true);
              }
          }
          else
          {
              rb.velocity = Vector3.zero;
              rb.isKinematic = true;
  
              transform.up = other.contacts[0].normal;
  
              DelayExplosion();
          }
  
          if (!explodeOnImpact) return;
  
          if (other.gameObject.CompareTag("KillPlane") || other.gameObject.CompareTag("Ground")) return;
      }
  
      private void DelayExplosion()
      {
          this.Invoke(nameof(Explode), explosionDelay);
      }
  
      private void Explode()
      {
          this.CancelInvoke();
  
          if(explosionPrefab == null)
          {
              Destroy(gameObject);
              Debug.Log("NOEXPLOSION");
              return;
          }
  
          GameObject explosionObj = Instantiate(explosionPrefab, transform.position, Quaternion.identity);
          Destroy(gameObject);
      }
  
      private void OnDrawGizmosSelected()
      {
          Gizmos.color = Color.red;
          Gizmos.DrawWireSphere(transform.position, proximityDistance);
      }
  }


  ```
</details>

## Game Design
The idea for the game wasn't mine but I really liked it from the beginning so i was motivated to make it to its best.  

Came up with a lot of ideas for creating the weapons that are used in the game.  

We were experimenting with a lot of different game modes, because of the time limit we didn't manage to create more of them.  

Took a part in the level design process, how to place everything that exists on the arenas.  

<img src="https://github.com/Pirat1001/Portfolio/blob/main/Images/lavaArena.png" width="500"/> <img src="https://github.com/Pirat1001/Portfolio/blob/main/Images/zombieArena.png" width="500"/>


### Also Worked With
Debugging and testing the game.

Planning and prioritizing with my group.

Took part of managing the game scope.
