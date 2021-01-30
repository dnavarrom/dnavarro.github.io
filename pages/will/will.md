# Will


## What is this

A world filled by creatures who respond to external and internal conditions to develop and survive.

## Demo 


You can see it in action [Here](/demo/will/index.html). Press spacebar to enable debug view. Enjoy.

## Basics

### World

The container that represents creatures world, boundaries and limits. 

![dnavarrm](/pages/will/img/world.gif)

### Survivor

this type of creature exists to survive and generate offspring, following (in some cases) some rules: 

* If I'm hungry, then I will eat food but only if I'm not in danger or reproducing
* If I'm in danger, I must escape/dodge and lost the predators
* If I'm an adult, and I'm not in danger and have enough energy (food), then I'll find a partner to reproduce
* If I'm during reproducing process and starting to be in danger/hunger, I may (or may not) interrupt coupling and move

![dnavarrm](/pages/will/img/creatures.png)

* Red creatures are adults
* Green creatures are old
* White creatures are young

### Predator

This type of creature hunts survivors. They are bigger and slower than most of the survivors.

![dnavarrm](/pages/will/img/predator.png)


### Reproduction System

Reproduction system uses genetic algorithm techniques which reflects the process of natural selection where the fittest individuals are selected for reproduction in order to produce offspring of the next generation. The rules are: 

* Both parents are adults
* Both parents are not in danger (not dodging)
* Both parents are well feeded ( energy > 50%)
* Both parents are from diferent gender
* Both parents are not sons of each other
* Both parents don't have common offspring
* Both parents are in their vision range
* Both parents meets minimum fertility conditions

When they find a partner and meets all the conditions, reproduction start and they will change color and behavior

![dnavarrm](/pages/will/img/reproduction.gif)


### Reproduction System Details

The definition of Genetic Algorithm can be found [here](https://towardsdatascience.com/introduction-to-genetic-algorithms-including-example-code-e396e98d8bf3). Basically, each creature in our population has genes that describes their caracteristics and behavior.

![dnavarrm](/pages/will/img/ga.png)


Each creature has their chromosome based on attriutes (genes):


| Attribute  | Description | Value Ranges |
| ----------- | ----------- | ------------ |
| speed           | Moving Speed | [0,2]    |
| visionRange     | Vision Range | [50,200] |
| turningSpeed    | Reaction Speed | [0,2]  |
| maxEnergy       | Energy to live | [0,10] |
| fertility       | Fertility ratio | [0,1] |  
| couplingDistance| Distance to copule | [10,20] |
| braveness       | Probability to take risks | [0,1] |


And the Chromosome which represents all the attributes: 


```javascript

let chromosome = {
    speed : 0,
    visionRange : 0,
    turningSpeed : 0,
    maxEnergy : 0,
    fertility : 0,
    copulingDistance : 0,
    braveness : 0
}

```

The genetic operators (mutation / crossover) are applied based on cofiguration parameters: 

```javascript
let geneticOperators : {
        crossoverProbabilityRate : 0.3,
        specificGeneCrossoverRate : 0.5,
        mutationProbabilityRate : 0.5,
        specificGeneMutationRate : 0.3
    }
```

So in each reproduction cycle, the reproduction system may execute crossover and/or mutation operators. The result is a new creature with the characteristics of both parents.

### Vision Range and Debug Information

Each creature has a vision range determined by their genes. They will interact with objects that are in their vision range, including predators, food and mates. Hud information shows the energy attribute (which its maximum value is determined by their genes).

![dnavarrm](/pages/will/img/vision-range.png)

### Family Members

Relations are represented by blue lines

![dnavarro](/pages/will/img/relationship.png)


### Dodging behavior

Some creatures wants to avoid danger

![dnavarrm](/pages/will/img/dodging.gif)


### Dying

Creatures have limited time to live. They should survive and reproduce before their time ends.

![dnavarrm](/pages/will/img/dying.gif)


## The Code

### Creatures


![dnavarrm](/pages/will/img/creature-class.png)


The Creature SuperClass has common attributes and behavior for all types, it has methods like: 

```javascript
move();          //movement
setDirection();  //movement
setPosition();   // movement
collectStats();  //all creature information
consumeEnergy(); //always
addLivingTime(); //when eat
addChildren();   // for reproduction system
getCurrentMate(); // for reproduction system
setCurrentMate(); // for reproduction system

```

### Survivor Class

The Survivor class extends Creature class and implements some particular behavior and attributtes

```javascript

findFood(foodInfo);     //
move();                 //override
getCurrentStatus();     //get reproduction status
startCoupling();        //reproduction system
endCoupling();          //reproduction system
interruptCoupling();    //reproduction system
reproduce();            //reproduction system
findMate();             //reproduction system
checkReproductionConditions();  //reproduction system
evadePredator();        
eat();

```

### Predator Class

The Predator class extends Creature class and implements some particular behavior and attributtes

```javascript
findSurvivors(survivorsInfo);
eat();

```

### World Class

World class is a PIXI Scene class (A scene is basically a Pixi Stage object, which can be paused/resumed/updated) that handles simulation status and creature (survivor and predator) logic and behavior. It also handles graphics engine logic and integrates Creature logic with graphics render logic

```javascript
init();                         //init food, survivors, predators
processSurvivor();              //handle survivor states (eat, dodge, reproduce)
processPredator();              //same for predator
processFood();                  //same for food
createNewSurvivors(population);  //renders new survivors after reproduction


```

### Graphic Engine - PIXI.JS

[PIXI.JS](http://www.pixijs.com/) is a rendering library that will allow you to create rich, interactive graphics, cross platform applications, and games without having to dive into the WebGL API or deal with browser and device compatibility.


![dnavarrm](/pages/will/img/sprite-class.png)


### Main.js

Based on PIXI.js [code sample](https://github.com/kittykatattack/learningPixi).

```javascript

//Setup Pixi and load the texture atlas files - call the `setup`
//function when they've loaded

function setup() {
  //Initialize the game sprites, set the game `state` to `play`
  //and start the 'gameLoop'

  world.init();

  //Load Scenes
  //Scene 1 : Splash
  //Scene 2 : Main Simulation
  //Scene 3: UI information
  //Scene 4: UI Controls
  
  stateManager = new StateManager(); //handle app states

}

function gameLoop(delta) {
  //Runs the current game `state` in a loop and renders the sprites
  state(delta);
  tick += 0.1;
}

function run(delta) {
  // All the simulation logic
  world.processSurvivor();
  world.processPredator();
  world.processFood();

  checkSimulationStatus();
}

function end() {
  //All the code that should run at the end of the game
}

```

### TODO: 

A lots of things to do: [TODO](https://github.com/dnavarrom/will/blob/master/todo.md)
