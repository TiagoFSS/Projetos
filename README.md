# Projetos
var utils = require('../lib/utils.js');

const MOVES = ['shoot', 'move'];
var DIRECTIONS = ["north", "east", "south", "west"];
const randomMove = () => {
  return Math.random() > 0.33 ? 'move' : DIRECTIONS[Math.floor(Math.random() * DIRECTIONS.length)];
};

const isVisible = (originalPosition = [], finalPosition = [], direction = []) => {
  switch (direction) {
    case DIRECTIONS[0]:
      return originalPosition[1] === finalPosition[1] && originalPosition[0] > finalPosition[0];
    case DIRECTIONS[1]:
      return originalPosition[0] === finalPosition[0] && originalPosition[1] < finalPosition[1];
    case DIRECTIONS[2]:
      return originalPosition[1] === finalPosition[1] && originalPosition[0] < finalPosition[0];
    case DIRECTIONS[3]:
      return originalPosition[0] === finalPosition[0] && originalPosition[1] > finalPosition[1];
    default:
      break;
  }
};
var getShortestDirection = (start, endArray) => {
  var reducedArray = endArray.reduce(
    (reduced, currentPosition) => {
      if (reduced[0] === -1 || utils.getDistance(start, currentPosition) < reduced[0]) {
        reduced[0] = utils.getDistance(start, currentPosition);
        reduced[1] = currentPosition;
      }

      return reduced;
    },
    [-1, 0]
  );

  return utils.fastGetDirection(start, reducedArray[1]);
};

const canKill = (currentPlayerState = {}, enemiesStates = []) => {
  return enemiesStates.some(enemyObject => {
    return enemyObject.isAlive &&
      isVisible(currentPlayerState.position, enemyObject.position, currentPlayerState.direction);
  });
};
var canDie = function(player, enemies) {
  return enemies
    .map(function(enemy) {
      return enemy.ammo > 0 && utils.isVisible(enemy.position, player.position, enemy.direction);
    })
    .filter(function(result) {
      return result === true;
    }).length > 0;
};
var isMovementSafe = function(action, player, enemies, map) {
  var futureState = JSON.parse(JSON.stringify(player));

  if (action === 'move') {
    switch (player.direction) {
      case DIRECTIONS[0]:
        if (futureState.position[0] > 0) {
          futureState.position[0]--;
        }
        break;
      case DIRECTIONS[1]:
        if (futureState.position[1] < map.gridSize) {
          futureState.position[1]++;
        }
        break;
      case DIRECTIONS[2]:
        if (futureState.position[0] < map.gridSize) {
          futureState.position[0]++;
        }
        break;
      case DIRECTIONS[3]:
        if (futureState.position[1] > 0) {
          futureState.position[1]--;
        }
        break;
      default:
        break;
    }
  }

  if (canDie(futureState, enemies)) {
    return false;
  } else {
    return true;
  }
};


var turnToKill = (originalPosition, positionArray) => {
  return DIRECTIONS.reduce((result, currentDirection) => {
    if (result) return result;
    return positionArray.reduce((resultPositions, currentEnemyPosition) => {
      if (resultPositions) return resultPositions;
      return utils.isVisible(originalPosition, currentEnemyPosition, currentDirection) ? currentDirection : null;
    }, null);
  }, null);
};

const player2 = {
  info: {
    name: "RATO",
    style: 2,
  },
  ai: (playerState, enemiesState, gameEnvironment) => {
    //Seu código aqui
    // Lembrando que a cada turno essa função vai ser executada
    // E ela tem que retornar um movimento
    // Seja atirar (shoot), mover(move), girar(south, west, east, north)
    var directionToAmmo;
    var directionToPlayer;
    var directionToenemieState

     if (utils.canKill(playerState, enemiesState) && playerState.ammo) return "shoot";
    
      if (playerState.ammo ) {
      directionToPlayer = turnToKill(playerState.position, enemiesState.map(el => el.position));
      if (directionToPlayer) { if(isMovementSafe)
        return directionToPlayer;
      }

      directionToPlayer = getShortestDirection(playerState.position, enemiesState.map(el => el.position));
      if (directionToPlayer !== playerState.direction && isMovementSafe ) return directionToPlayer;
      if (isMovementSafe) return "move";
    }
    
  
    if (gameEnvironment.ammoPosition.length ) {
      directionToAmmo = utils.fastGetDirection(playerState.position, gameEnvironment.ammoPosition[0]);

      if (directionToAmmo !== playerState.direction && isMovementSafe ) return directionToAmmo;
       if(isMovementSafe) return 'move';
    }
     if(canDie ){return isMovementSafe
      }
      

      
  
return utils.safeRandomMove()
    
  
},
}
module.exports = player2;
