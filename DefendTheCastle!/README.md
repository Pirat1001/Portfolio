# Defend The Castle!  

![image](https://github.com/Pirat1001/Portfolio/blob/main/Images/GameLogo.png)  

Defend The Castle is a solo game where you're playing as an archer,
your main goal is to shoot swarming enemies to defend the castle, the game gets harder with each round!  

## Defend The Castle!
This was a solo project that I've created which means I am the only contributor. I will mention a couple of things that made a great challenge and maybe what i'm proud of, myself first time working with UE5.  

### Landscape & Foliage 
The thing I started with, beginning my project.  

<img src="https://github.com/Pirat1001/Portfolio/blob/main/Images/Landscape1.png" width="320"/> <img src="https://github.com/Pirat1001/Portfolio/blob/main/Images/Landscape2.png" width="320"/> <img src="https://github.com/Pirat1001/Portfolio/blob/main/Images/Landscape3.png" width="320"/>  

Creating the different materials used on the landscape like Rocks(mountains)/Grass/Dirt/etc.  

With UE nodes/blueprints creating the functionality for each ground material, where should grass appear and where the rocks should appear.  

Sculpting the landscape was done by hand(which took a while).  

Creating a navmeshbound where within the foliage is going to spawn.  

Making the logic for cooperation between landscape and foliage, in order for the foliage to spawn in correct areas, ex. trees on grass, bushes on grass, the rocks to appear on rock landscape material, etc.  

### Character
_The character model is sparrow from the game paragon, the character model is free to download on epic games UE marketplace._  

<img src="https://github.com/Pirat1001/Portfolio/blob/main/Images/CharacterImage1.png" width="500"/> <img src="https://github.com/Pirat1001/Portfolio/blob/main/Images/CharacterImage2.png" width="500"/>  

Although the character from marketplace brings its own Blueprint class and Animation Blueprint, in order to learn i deleted those and created my own from scratch.  

Making a nice looking rotation of the character while aiming with the bow(upper body turns while lower stays still)  

Implementing the different animations accessible through the character like firing, moving, jumping, etc.  

Creating a healthBar UI and functionality for the character.  

Player is spawning arrow blueprints when firing an arrow. I've created the trajectory the arrow is taking, adjusted the physics for the arrow to look realistic and how the arrow should affect other actors, ex. actor explodes and destroys itself when hitting a castle wall, deals damage to an enemy, etc.  

### Game Mode
_Creating the Game Mode wasn't only programming it but a lot of design thinking as well_  

The games game mode is wave defense.  

Making a data table that contains specific information about a wave, ex. how many enemies?, what sorts of enemies?, etc.  

Defining what an enemy in enemy pool is and building the pool by reading the data from data table, then getting random enemies from the pool to spawn.  

All waves have a different amount of enemies that are supposed to spawn, you have UI showing you how much enemies are left/how many you've killed. After all the enemies in the specific wave have died, there's a short break/countdown until the next one starts.  

Game mode also needs to get all the spawners using a loop, there are more than one spawners in the world, so the enemies can spawn at different locations. When spawning an enemy it picks a random spawner from spawners list, more spawners get unlocked with progressing through the game.  

<details>
  <summary>Dropdown to see a few BP nodes from UE5 project</summary>

  <img src="https://github.com/Pirat1001/Portfolio/blob/main/Images/BP_gameMode1.png" width="320"/> <img src="https://github.com/Pirat1001/Portfolio/blob/main/Images/BP_gameMode2.png" width="320"/> <img src="https://github.com/Pirat1001/Portfolio/blob/main/Images/BP_gameMode3.png" width="320"/>  
</details>
