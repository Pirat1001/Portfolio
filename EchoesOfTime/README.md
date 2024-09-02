# Echoes Of Time

![image](https://github.com/Pirat1001/Portfolio/blob/main/Images/EchoesOfTimeLogo.png)  

Embark on a riveting journey with "Echoes of Time", a captivating narrative-based story game that puts you in the shoes of Amber, a young girl facing the inevitability of her own demise. Armed with a mysterious old radio that can transport her to diverse memories, you'll unravel the mystery surrounding her fate. Every choice you make carries weight, as one wrong question could stand between you and the revelation of life's most profound mystery. Delve into the echoes of the past, where your decisions shape the course of Amber's destiny in this immersive and emotionally charged adventure.

Are you ready to uncover the secrets that transcend time?  

[Itch.io](https://yrgo-game-creator.itch.io/echoes-of-time)  
[Github Repository](https://github.com/Achban1/Echoes_Of_Time)  

## Contribution  

### UI  
Created the menu for the game  

Pause screen  

Adjusting time machine placement and functionality  

Credits screen in the menu  

Options tab  

<details>
  <summary>Code I wrote for Main Menu</summary>

  ```csharp
    using UnityEngine;
    using UnityEngine.SceneManagement;
    
    public class MainMenuScript : MonoBehaviour
    {
        public GameObject ImageHowToPlay;
        public GameObject ImageOptions;
        public GameObject boolsForYarnSpinner;
        public GameObject buttonPanel;
        public GameObject creditsCanvas;
        public string objectNameToDestroy = "BoolsForYarnSpinner"; 
    
        private void Start()
        {
            GameObject objectToDestroy = GameObject.Find(objectNameToDestroy);
            creditsCanvas.SetActive(false);
    
            if (objectToDestroy != null)
            {
                Destroy(objectToDestroy);
            }
            else
            {
                Debug.LogWarning("Object with name '" + objectNameToDestroy + "' not found.");
            }
        }
        public void StartGame()
        {  
            SceneManager.LoadScene("Prolouge");
        }
    
        public void ShowHowToPlay()
        {
            ImageHowToPlay.SetActive(true);
            buttonPanel.SetActive(false);
        }
    
        public void HideHowToPlay()
        {
            ImageHowToPlay.SetActive(false);
            buttonPanel.SetActive(true);
        }
    
        public void ShowOptions()
        {
            ImageOptions.SetActive(true);
            buttonPanel.SetActive(false);
        }
    
        public void HideOptions()
        {
            ImageOptions.SetActive(false);
            buttonPanel.SetActive(true);
        }
    
        public void SwitchToCredits()
        {
            if(buttonPanel.activeSelf == true)
            {
                buttonPanel.SetActive(false);
                creditsCanvas.SetActive(true);
            }
            else 
            {
                buttonPanel.SetActive(true);
                creditsCanvas.SetActive(false);
            }
        }
    
    
        public void ExitGame()
        {
    #if UNITY_EDITOR
            UnityEditor.EditorApplication.isPlaying = false;
    #else
             Application.Quit();
    #endif
        }
    }
  ```
</details>

### VFX  
Scene transition wipe effect  

Working with shaders  

The dynamic lights on the time machine  

Other effects visible in the game  

<details>
  <summary>Code I wrote for the lights on the time machine</summary> 

  ```csharp
    using System.Collections;
    using UnityEngine;
    using UnityEngine.SceneManagement;
    
    public class LightManagement : MonoBehaviour
    {
        public GameObject[] tLights;
        public static LightManagement instance;
        private GameObject knobObject;
        private KnobScript knob;
        private ParticleSystem.MainModule[] mainModules;
        private bool[] sceneVisitedArray;
        private bool[] isSceneUnlocked;
        private bool[] shouldBlinkArray;
        public bool keepBlinking = false;
        private bool canCallBlinking = true;
    
        public bool sceneNameSet;
        public bool scenesMatch;
        public float timer = 0f;
        private float angle;
        private int sceneIdentifier;
        private Scene activeScene;
        public float colorChangeInterval = 2f;
        public float colorChangeDuration = 1.5f;
        public Color startColor = new Color(255f / 255f, 255f / 255f, 76f / 255f, 103f / 255f);
        public Color endColor = new Color(84f / 255f, 84f / 255f, 80f / 255f, 72f / 255f);
        public Color pointColorStart = new Color(136f / 255f, 243f / 255f, 153f / 255f, 72 / 255f);
        public Color unavailableColor = new Color(246f / 255f, 107f / 255f, 110f / 255f, 72f / 255f);
    
        void Start()
        {
            var bools = BoolsForYarnSpinnerScript.Instance;
            activeScene = SceneManager.GetActiveScene();
            mainModules = new ParticleSystem.MainModule[tLights.Length];
            sceneNameSet = false;
            scenesMatch = false;
            knobObject = GameObject.Find("Knob");
            knob = knobObject.GetComponent<KnobScript>();
            angle = knob.angle;
            shouldBlinkArray = new bool[]
            {
                bools.light1Blink,
                bools.light2Blink,
                bools.light3Blink,
                bools.light4Blink,
                bools.light5Blink,
                bools.light6Blink
            };
    
            for (int i = 0; i < tLights.Length; i++)
            {
                mainModules[i] = tLights[i].GetComponent<ParticleSystem>().main;
            }
    
            for(int i = 0;i < shouldBlinkArray.Length; i++)
            {
                if(shouldBlinkArray[i].ToString() == activeScene.name)
                {
                    shouldBlinkArray[i] = false;
                }
            }
    
        }
    
        void Update()
        {
            timer += Time.deltaTime;
            var bools = BoolsForYarnSpinnerScript.Instance;
            sceneIdentifier = knob.sceneIdentifier;
    
            if (bools.sceneName == activeScene.name)
            {
                scenesMatch = true;
            }
            else
            {
                scenesMatch = false;
            }
    
            sceneVisitedArray = new bool[]
            {
                bools.isScene1Visited,
                bools.isScene2Visited,
                bools.isScene3Visited,
                bools.isScene4Visited,
                bools.isScene5Visited,
                bools.isScene6Visited
            };
    
            isSceneUnlocked = new bool[]
            {
                bools.scene1Unlocked,
                bools.scene2Unlocked,
                bools.scene3Unlocked,
                bools.scene4Unlocked,
                bools.scene5Unlocked,
                bools.scene6Unlocked
            };
    
            shouldBlinkArray = new bool[]
            {
                bools.light1Blink,
                bools.light2Blink,
                bools.light3Blink,
                bools.light4Blink,
                bools.light5Blink,
                bools.light6Blink
            };
    
            if(canCallBlinking)
            {
                StartCoroutine(WaitAndStartBlinking(sceneVisitedArray, isSceneUnlocked, shouldBlinkArray));
            }
    
            
            for (int i = 0; i < mainModules.Length; i++)
            {
                if (i == sceneIdentifier) 
                {
                    OnPointColorSwap(i, sceneVisitedArray[i]);
                }
                else
                {
                    SwapParticleColor(i, sceneVisitedArray[i]);
                }
            }
            
            if (keepBlinking)
            {
                CheckBlinkingArray(shouldBlinkArray);
            }
        }
    
        IEnumerator WaitAndStartBlinking(bool[] isVisit, bool[] isUnlocked, bool[] shouldBlink)
        {
            canCallBlinking = false;
            yield return new WaitForSeconds(1.5f);
            SceneBlink(isVisit, isUnlocked, shouldBlink);
            canCallBlinking = true;
        }
    
        public void CheckBlinkingArray(bool[] blinking)
        {
            for (int i = 0; i < blinking.Length; i++)
            {
                if (blinking[i])
                {
                    Debug.Log(blinking[i].ToString());
                    LightsStartBlinking(i, blinking);
                }
            }
        }
    
        public void OnPointColorSwap(int checkNum, bool visited)
        {
            if(visited)
            {
                mainModules[checkNum].startColor = pointColorStart;
            }
            else
            {
                mainModules[checkNum].startColor = unavailableColor;
            }
        }
    
        public void SwapParticleColor(int moduleIndex, bool visited)
        {
            if (visited)
            {
                mainModules[moduleIndex].startColor = startColor;
            }
            else
            {
                mainModules[moduleIndex].startColor = endColor;
            }
        }
        
        public void LightsStartBlinking(int i, bool[] blinking)
        {
            SetSceneName();
            StartCoroutine(LerpColor(startColor, endColor, colorChangeDuration, i, blinking));
        }
    
        private void SetParticleColor(Color color, ParticleSystem.MainModule ps)
        {
            ps.startColor = color;
        }
    
        private IEnumerator LerpColor(Color start, Color end, float duration, int i, bool[] blinking)
        {
            float timeElapsed = 0f;
            ParticleSystem.MainModule main = mainModules[i];
    
            while (blinking[i])
            {
                timeElapsed = 0;
    
                while (timeElapsed < duration)
                {
                    timeElapsed += Time.deltaTime;
                    float t = Mathf.Clamp01(timeElapsed / duration);
    
                    if(i == sceneIdentifier)
                    {
                        Color lerpedColor = Color.Lerp(pointColorStart, end, t);
                        SetParticleColor(lerpedColor, main);
                    }
                    else
                    {
                        Color lerpedColor = Color.Lerp(start, end, t);
                        SetParticleColor(lerpedColor, main);
                    }
    
                    yield return null;
                }
    
                SetParticleColor(end, main);
            }
        }
    
        public void SetSceneName()
        {
            if (!sceneNameSet)
            {
                BoolsForYarnSpinnerScript.Instance.sceneName = activeScene.name;
                sceneNameSet = true;
            }
        }
    
        void SceneBlink(bool[] isVisited, bool[] isUnlocked, bool[] shouldBlink)
        {
            for(int i = 0; i < isVisited.Length; i++)
            {
                if ((isUnlocked[i] && !isVisited[i]) || shouldBlink[i] == true)
                {
                    keepBlinking = true;
                    break;
                }
                else
                {
                    keepBlinking = false;
                }
            }
        }
    }
  ```
</details>

### Other contributions  
Helping with creating time machine effect  

Bug fixing the game  

Game Design  


