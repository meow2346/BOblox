import * as THREE from 'three';
import { OrbitControls } from 'https://cdn.jsdelivr.net/npm/three@0.150.1/examples/jsm/controls/OrbitControls.js';

import { MTLLoader } from 'https://cdn.jsdelivr.net/npm/three@0.150.1/examples/jsm/loaders/MTLLoader.js';

import { OBJLoader } from 'https://cdn.jsdelivr.net/npm/three@0.150.1/examples/jsm/loaders/OBJLoader.js';

import { DoubleSide, TextureLoader } from 'three';
import { TweenRotation, TweenPosition, positionTo, rotationTo, ZPT, XPT, YPT, ZRT, XRT, YRT } from '/Tween.js';
// variables
const Canvas = document.getElementById("Game")
var socket = io();

function wait(Time) {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve('resolved');
    }, Time * 1000);
  });
}

var keyEe = {
  a: false,
  s: false,
  d: false,
  w: false,
  space: false,
};

const Avatar = {
  Face: localStorage.getItem("Face"),
  HeadP: localStorage.getItem("Head"),
  TorsoP: localStorage.getItem("Torso"),
  LArmP: localStorage.getItem("leftArm"),
  RArmP: localStorage.getItem("rightArm"),
  RLegP: localStorage.getItem("rightLeg"),
  LLegP: localStorage.getItem("leftLeg"),
  HeadC: localStorage.getItem("HeadC"),
  TorsoC: localStorage.getItem("TorsoC"),
  LArmC: localStorage.getItem("LArmC"),
  RArmC: localStorage.getItem("RArmC"),
  LLegC: localStorage.getItem("LLegC"),
  RLegC: localStorage.getItem("RLegC")
}

const LocalCharacter = {
  maxZoom: 50,
  sensitivity: 1
};

var PlrND = false

const Character = {
  LoadedChar: false,
  Username: "Guest",
  CharStandY: 1,
  WalkSpeed: 3.5,
  switchMov: false,
  GravitySpeed: 10,
  standing: true,
  walking: false,
  jumping: false,
  Player: 0,
  FP: false,
  FPH: false
};

$(function() {
  socket.on('plr', function(Plr) {
    if (PlrND == false) {
      Character.Player = Plr
      PlrND = true
    }
    console.log(Plr + " Has joined")
  });
})


const loadingManager = new THREE.LoadingManager();

loadingManager.onError = function(url) {
  alert("Boblox has ran into a problem loading")
}

function lded() {
  document.getElementById("LD-BG").style.display = "none"
  controls.target = Cambox.position
}

loadingManager.onLoad = function() {
  console.log("Loaded")
  document.getElementById("LD-BG").style.transition = "0.4s"
  document.getElementById("LD-BG").style.opacity = 0
  setTimeout(lded,400)
}

// scene
const scene = new THREE.Scene();

// camera
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.set(0, 6, 10);
camera.rotation.order = "YXZ"
const listener = new THREE.AudioListener();
camera.add(listener);



// renderer
const renderer = new THREE.WebGLRenderer({ canvas: Canvas });
renderer.setClearColor(0x4fb8b4, 1);

// enabling shadows
renderer.shadowMap.enabled = true;
renderer.shadowMapSoft = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;

document.body.appendChild(renderer.domElement);
renderer.setSize(window.innerWidth, window.innerHeight);

// orbit controls
const controls = new OrbitControls(camera, renderer.domElement);
controls.zoomSpeed = 4
controls.rotateSpeed = LocalCharacter.sensitivity
controls.enablePan = false
controls.minDistance = 0.5
controls.maxDistance = LocalCharacter.maxZoom
controls.mouseButtons = {
  MIDDLE: THREE.MOUSE.DOLLY,
  RIGHT: THREE.MOUSE.ROTATE
}

// detect collisions with camera

const lineMaterial = new THREE.LineBasicMaterial({
    color: 0x0000ff, transparent: true, opacity: 0,
})

var points = []
points[0] = new THREE.Vector3(-0.1, 0, 0)
points[1] = new THREE.Vector3(0.1, 0, 0)
let lineGeometry = new THREE.BufferGeometry().setFromPoints(points)
const xLine = new THREE.Line(lineGeometry, lineMaterial)
scene.add(xLine)
points[0] = new THREE.Vector3(0, -0.1, 0)
points[1] = new THREE.Vector3(0, 0.1, 0)
lineGeometry = new THREE.BufferGeometry().setFromPoints(points)
const yLine = new THREE.Line(lineGeometry, lineMaterial)
scene.add(yLine)
points[0] = new THREE.Vector3(0, 0, -0.1)
points[1] = new THREE.Vector3(0, 0, 0.1)
lineGeometry = new THREE.BufferGeometry().setFromPoints(points)
const zLine = new THREE.Line(lineGeometry, lineMaterial)
scene.add(zLine)


const raycaster = new THREE.Raycaster()
const sceneMeshes = new Array()
let dir = new THREE.Vector3()
let intersects = []
controls.addEventListener('change', function () {
    xLine.position.copy(controls.target)
    yLine.position.copy(controls.target)
    zLine.position.copy(controls.target)
    dir.subVectors(camera.position, controls.target).normalize()
    raycaster.set(controls.target, dir.subVectors(camera.position, controls.target).normalize())
    intersects = raycaster.intersectObjects(sceneMeshes, false)
    if (intersects.length > 0) {
        if (intersects[0].distance < controls.target.distanceTo(camera.position)) {
            camera.position.copy(intersects[0].point)
        }
    }
})




// end camera collisions

const CPBox = new THREE.Mesh(new THREE.BoxGeometry(2, 5, 1), new THREE.MeshStandardMaterial({ color: 0x7d7d7d, transparent: true, opacity: 0 }));
CPBox.position.y = 2
scene.add(CPBox);

const Cambox = new THREE.Mesh(new THREE.BoxGeometry(1, 1, 1), new THREE.MeshStandardMaterial({ color: 0x7d7d7d, transparent: true, opacity: 0 }));
Cambox.position.y = 2
scene.add(Cambox);


function checkTouching(a, d) {
  let b1 = a.position.y - a.geometry.parameters.height / 2;
  let t1 = a.position.y + a.geometry.parameters.height / 2;
  let r1 = a.position.x + a.geometry.parameters.width / 2;
  let l1 = a.position.x - a.geometry.parameters.width / 2;
  let f1 = a.position.z - a.geometry.parameters.depth / 2;
  let B1 = a.position.z + a.geometry.parameters.depth / 2;
  let b2 = d.position.y - d.geometry.parameters.height / 2;
  let t2 = d.position.y + d.geometry.parameters.height / 2;
  let r2 = d.position.x + d.geometry.parameters.width / 2;
  let l2 = d.position.x - d.geometry.parameters.width / 2;
  let f2 = d.position.z - d.geometry.parameters.depth / 2;
  let B2 = d.position.z + d.geometry.parameters.depth / 2;
  if (t1 < b2 || r1 < l2 || b1 > t2 || l1 > r2 || f1 > B2 || B1 < f2) {
    return false;
  }
  
  if(f1<B2) {
    
  }
  
  if(t1>b2) {
    if(b2 > -2.5) {
      if(t1 < 0) {
        Body.position.y = t1 + 0.5
        console.log("t1: "+ t1 + " to b2: " + b2)
      }
    }
  }
  /*if(l1>r2) {
    console.log("touching +X")
  }*/
  return true;
}

const collidableMeshList = [];

// group

const Body = new THREE.Group()
const Head = new THREE.Group()

function textureMesh(OBJ,xR,zR,TXTR) {
  const texture = new THREE.TextureLoader().load( TXTR );
  texture.wrapS = THREE.RepeatWrapping;
  texture.wrapT = THREE.RepeatWrapping;
  texture.repeat.set( 10 / xR, 8 / zR );
  var TXTRTar = OBJ.clone()
  TXTRTar.material = new THREE.MeshBasicMaterial( { color: 0xffffff, transparent: true, opacity: 1, map: texture } );
  scene.add(TXTRTar)
}

const Baseplate = new THREE.Mesh(new THREE.BoxGeometry(10, 3, 8), new THREE.MeshStandardMaterial({ color: 0x7d7d7d }));
Baseplate.position.y = -3
Baseplate.castShadow = true;
Baseplate.receiveShadow = true;
scene.add(Baseplate);
sceneMeshes.push(Baseplate)
collidableMeshList.push(Baseplate);

textureMesh(Baseplate,1.43,1.43,"Things/Game/textures/topStuds.png")

const platform1 = new THREE.Mesh(new THREE.BoxGeometry(8, 3, 8), new THREE.MeshStandardMaterial({ color: 0x7d7d7d }));
platform1.position.y = -3
platform1.position.z = 10
platform1.castShadow = true;
platform1.receiveShadow = true;
scene.add(platform1);
sceneMeshes.push(platform1)
collidableMeshList.push(platform1);
const platform2 = new THREE.Mesh(new THREE.BoxGeometry(8, 3, 8), new THREE.MeshStandardMaterial({ color: 0x7d7d7d }));
platform2.position.y = -3
platform2.position.z = -10
platform2.castShadow = true;
platform2.receiveShadow = true;
scene.add(platform2);
sceneMeshes.push(platform2)
collidableMeshList.push(platform2);
const Pillar = new THREE.Mesh(new THREE.BoxGeometry(1, 8, 1), new THREE.MeshStandardMaterial({ color: 0x7d7d7d }));
Pillar.position.y = 2
Pillar.position.z = -10
Pillar.castShadow = true;
Pillar.receiveShadow = true;
scene.add(Pillar);
sceneMeshes.push(Pillar)
collidableMeshList.push(Pillar);

function cc() {
  let CN = 0
  requestAnimationFrame(cc)
  var colliding = false
  for(let i=0; i<collidableMeshList.length; i++) {
    if(checkTouching(collidableMeshList[CN], CPBox) || colliding == true)     {
      Character.standing = true
      colliding = true
    } else {
      Character.standing = false
    }
    CN += 1
  }
}

let Torso;
const objLoader = new OBJLoader(loadingManager)
objLoader.load(
  'Things/Avatar/BodyParts/' + Avatar.TorsoP + '/torso.obj',
  (object) => {
    Torso = object;
    object.traverse(function(child) {

      if (child instanceof THREE.Mesh) {

        child.material = new THREE.MeshStandardMaterial({ color: Avatar.TorsoC });
        child.castShadow = true;
        child.receiveShadow = true;

      }

    });
    Torso.position.y = 2.5
    Body.add(Torso)
  },
)

function cpbox1() {
  requestAnimationFrame(cpbox1)
  CPBox.position.y = Body.position.y + 2
  CPBox.position.x = Body.position.x
  CPBox.position.z = Body.position.z
  CPBox.rotation.x = Body.rotation.x
  CPBox.rotation.z = Body.rotation.z
  CPBox.rotation.y = Body.rotation.y
}
cpbox1()

function camboxD() {
  requestAnimationFrame(camboxD)
  Cambox.position.y = Body.position.y + 2.5
  Cambox.position.x = Body.position.x
  Cambox.position.z = Body.position.z
}
camboxD()

const Face = new THREE.TextureLoader().load( 'Things/Avatar/Faces/' + Avatar.Face + '.png' );

let HeadT;
objLoader.load(
    'Things/Avatar/BodyParts/Default/head.obj',
    (object) => {
        HeadT = object;
        object.traverse( function ( child ) {
    
        if ( child instanceof THREE.Mesh ) {

          child.material = new THREE.MeshStandardMaterial({color:0xfff200, map: Face, transparent: true,});
          child.castShadow = true;
          child.receiveShadow = true;
          

        }

        } );
      HeadT.position.y = 4
      Head.add(HeadT)
    },
)

let HeadOBJ;
objLoader.load(
  'Things/Avatar/BodyParts/Default/head.obj',
  (object) => {
    HeadOBJ = object;
    object.traverse(function(child) {

      if (child instanceof THREE.Mesh) {

        child.material = new THREE.MeshStandardMaterial({ color: Avatar.HeadC });
        child.castShadow = true;
        child.receiveShadow = true;


      }

    });
    HeadOBJ.position.y = 4
    Head.add(HeadOBJ)
  },
)

Body.add(Head)



let leftArm;
objLoader.load(
  'Things/Avatar/BodyParts/' + Avatar.LArmP + '/leftArm.obj',
  (object) => {
    leftArm = object;
    object.traverse(function(child) {

      if (child instanceof THREE.Mesh) {

        child.material = new THREE.MeshStandardMaterial({ color: Avatar.RArmC });
        child.castShadow = true;
        child.receiveShadow = true;

      }

    });
    leftArm.position.y = 2.5
    leftArm.position.x = -1.5
    Body.add(leftArm)
  },
)

let rightArm;
objLoader.load(
  'Things/Avatar/BodyParts/' + Avatar.RArmP + '/rightArm.obj',
  (object) => {
    rightArm = object;
    object.traverse(function(child) {

      if (child instanceof THREE.Mesh) {

        child.material = new THREE.MeshStandardMaterial({ color: Avatar.LArmC });
        child.castShadow = true;
        child.receiveShadow = true;

      }

    });
    rightArm.position.y = 2.5
    rightArm.position.x = 1.5
    Body.add(rightArm)
  },
)

let LeftLeg;
objLoader.load(
  'Things/Avatar/BodyParts/' + Avatar.LLegP + '/leftLeg.obj',
  (object) => {
    LeftLeg = object;
    object.traverse(function(child) {

      if (child instanceof THREE.Mesh) {

        child.material = new THREE.MeshStandardMaterial({ color: Avatar.LLegC });
        child.castShadow = true;
        child.receiveShadow = true;

      }

    });
    LeftLeg.position.y = 0.5
    LeftLeg.position.x = -0.5
    Body.add(LeftLeg)
  },
)

let RightLeg;
objLoader.load(
  'Things/Avatar/BodyParts/' + Avatar.RLegP + '/rightLeg.obj',
  (object) => {
    RightLeg = object;
    object.traverse(function(child) {

      if (child instanceof THREE.Mesh) {

        child.material = new THREE.MeshStandardMaterial({ color: Avatar.RLegC,});
        child.castShadow = true;
        child.receiveShadow = true;

      }
    });
    RightLeg.position.y = 0.5
    RightLeg.position.x = 0.5
    Body.add(RightLeg)
  },
)


const idV = new THREE.Mesh(new THREE.BoxGeometry(1, 1, 1), new THREE.MeshStandardMaterial({ color: 0x7d7d7d }));
idV.castShadow = true;
idV.receiveShadow = true;
idV.position.y = 2394999
scene.add(idV);

// light
const light = new THREE.AmbientLight(0x828282); // soft white light
scene.add(light);

let Sun = new THREE.DirectionalLight(0x8be0dd, 1);
Sun.position.set(-250, 300, -50);
Sun.target.position.set(0, 0, 0);
Sun.castShadow = true;
Sun.intensity = 1.5;
Sun.shadow.mapSize.width = 4096
Sun.shadow.mapSize.Height = 4096
Sun.shadow.camera.left = -500;
Sun.shadow.camera.right = 500;
Sun.shadow.camera.top = 500;
Sun.shadow.camera.bottom = -500;
scene.add(Sun);

// adds

scene.add(Body)

var cycleAnim = false
function CA() {
  cycleAnim = !cycleAnim
}
setInterval(CA,300)

function animation() {
  if(cycleAnim == false && Character.standing == true) {
    rotationTo(rightArm,0.8,0,0,0.02,false)
    ZPT(rightArm,0.4,0.02,true)
    rotationTo(leftArm,0.8,0,0,0.02,true)
    ZPT(leftArm,0.4,0.02,false)
    rotationTo(LeftLeg,0.8,0,0,0.02,false)
    ZPT(LeftLeg,0.8,0.02,true)
    rotationTo(RightLeg,0.8,0,0,0.02,true)
    ZPT(RightLeg,0.8,0.02,false)
  }
  if(cycleAnim == true && Character.standing == true) {
    rotationTo(rightArm,0.8,0,0,0.02,true)
    ZPT(rightArm,0.4,0.02,false)
    rotationTo(leftArm,0.8,0,0,0.02,false)
    ZPT(leftArm,0.4,0.02,true)
    rotationTo(LeftLeg,0.8,0,0,0.02,true)
    ZPT(LeftLeg,0.8,0.02,false)
    rotationTo(RightLeg,0.8,0,0,0.02,false)
    ZPT(RightLeg,0.8,0.02,true)
  }
}



// Scripts

function reportWindowSize() {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth, window.innerHeight);
}

window.onresize = reportWindowSize;

function Keys() {
  requestAnimationFrame(Keys)
  if (keyEe.w == true) {
    Body.position.z -= Character.WalkSpeed / 20 * Math.cos(camera.rotation.y)
    Body.position.x -= Character.WalkSpeed / 20 * Math.sin(camera.rotation.y)
    camera.position.x -= Character.WalkSpeed / 20 * Math.sin(camera.rotation.y)
    camera.position.z -= Character.WalkSpeed / 20 * Math.cos(camera.rotation.y)
    if(Character.FP == false) {
      Body.rotation.y = camera.rotation.y
    }
    Character.walking = true
  }
  if (keyEe.a == true) {
    Body.position.z += Character.WalkSpeed / 20 * Math.sin(camera.rotation.y)
    Body.position.x -= Character.WalkSpeed / 20 * Math.cos(camera.rotation.y)
    camera.position.x -= Character.WalkSpeed / 20 * Math.cos(camera.rotation.y)
    camera.position.z += Character.WalkSpeed / 20 * Math.sin(camera.rotation.y)
    if(Character.FP == false) {
      Body.rotation.y = camera.rotation.y + 1.55
    }
    Character.walking = true
  }
  if (keyEe.d == true) {
    Body.position.z -= Character.WalkSpeed / 20 * Math.sin(camera.rotation.y)
    Body.position.x += Character.WalkSpeed / 20 * Math.cos(camera.rotation.y)
    camera.position.x += Character.WalkSpeed / 20 * Math.cos(camera.rotation.y)
    camera.position.z -= Character.WalkSpeed / 20 * Math.sin(camera.rotation.y)
    if(Character.FP == false) {
      Body.rotation.y = camera.rotation.y - 1.55
    }
    Character.walking = true
  }
  if (keyEe.s == true) {
    Body.position.z += Character.WalkSpeed / 20 * Math.cos(camera.rotation.y)
    Body.position.x += Character.WalkSpeed / 20 * Math.sin(camera.rotation.y)
    camera.position.x += Character.WalkSpeed / 20 * Math.sin(camera.rotation.y)
    camera.position.z += Character.WalkSpeed / 20 * Math.cos(camera.rotation.y)
    if(Character.FP == false) {
      Body.rotation.y = camera.rotation.y - 3.1
    }
    Character.walking = true
  }
  if(keyEe.space == true) {
    if(Character.standing == true && Character.jumping == false) {
      jump()
    }
  }
  if (keyEe.s == false && keyEe.w == false && keyEe.a == false && keyEe.d == false ) {
    Character.walking = false
  }
}

let fallV = 0

 async function localCharPCode() {
  Sun.position.set(Body.position.x + -250, Body.position.y + 300, Body.position.z + -50);
  if (Character.standing == false) {
    if(fallV < 0.5) {
      fallV += 0.001
      await wait(fallV / 2)
    }
    Body.position.y -= fallV
    camera.position.y -= fallV
  } else {
    fallV = 0
  }
}

async function noWaitLocal() {
    if(Character.walking == true && Character.jumping == false) {
    animation()
  } else {
    rotationTo(leftArm,0,0,0,0.02,false)
    ZPT(leftArm,0,0.02,true)
    rotationTo(leftArm,0,0,0,0.02,true)
    ZPT(leftArm,0,0.02,false)
    rotationTo(rightArm,0,0,0,0.02,false)
    ZPT(rightArm,0,0.02,true)
    rotationTo(rightArm,0,0,0,0.02,true)
    ZPT(rightArm,0,0.02,false)
    rotationTo(LeftLeg,0,0,0,0.02,false)
    ZPT(LeftLeg,0,0.02,true)
    rotationTo(LeftLeg,0,0,0,0.02,true)
    ZPT(LeftLeg,0,0.02,false)
    rotationTo(RightLeg,0,0,0,0.02,false)
    ZPT(RightLeg,0,0.02,true)
    rotationTo(RightLeg,0,0,0,0.02,true)
    ZPT(RightLeg,0,0.02,false)
  }

   // camera

       if(Character.FP == true) {
      await Canvas.requestPointerLock();
    }
}

setInterval(noWaitLocal)

function zoom(event) {
  event.preventDefault();
   if(controls.getDistance() > 1) {
     Character.FPH = false
   } else if(Character.FPH == false) {
     controls.maxDistance = 0
     controls.enabled = false
     Character.FP = true
    if(Character.FP == true) {
     camera.position.y = Cambox.position.y
     camera.position.x = Cambox.position.x
     camera.position.z = Cambox.position.z
    }

    LeftLeg.traverse(function(child) {
      if (child instanceof THREE.Mesh) {
        child.material = new THREE.MeshStandardMaterial({ color: Avatar.LLegC, transparent: true, opacity: 0});
      }
    });
    RightLeg.traverse(function(child) {
      if (child instanceof THREE.Mesh) {
        child.material = new THREE.MeshStandardMaterial({ color: Avatar.RLegC, transparent: true, opacity: 0});
      }
    });
    leftArm.traverse(function(child) {
      if (child instanceof THREE.Mesh) {
        child.material = new THREE.MeshStandardMaterial({ color: Avatar.LArmC, transparent: true, opacity: 0});
      }
    });
    rightArm.traverse(function(child) {
      if (child instanceof THREE.Mesh) {
        child.material = new THREE.MeshStandardMaterial({ color: Avatar.RArmC, transparent: true, opacity: 0});
      }
    });
    HeadOBJ.traverse(function(child) {
      if (child instanceof THREE.Mesh) {
        child.material = new THREE.MeshStandardMaterial({ color: Avatar.HeadC, transparent: true, opacity: 0});
      }
    });
    Torso.traverse(function(child) {
      if (child instanceof THREE.Mesh) {
        child.material = new THREE.MeshStandardMaterial({ color: Avatar.TorsoC, transparent: true, opacity: 0});
      }
    });
   }
  console.log(event.deltaY)
  if(Character.FP = true && event.deltaY == 100) {
    Character.FP = false
    Character.FPH = true
    controls.enabled = true
    document.exitPointerLock()
    controls.maxDistance = LocalCharacter.maxZoom
    LeftLeg.traverse(function(child) {
      if (child instanceof THREE.Mesh) {
        child.material = new THREE.MeshStandardMaterial({ color: Avatar.LLegC,});
      }
    });
    RightLeg.traverse(function(child) {
      if (child instanceof THREE.Mesh) {
        child.material = new THREE.MeshStandardMaterial({ color: Avatar.RLegC,});
      }
    });
    leftArm.traverse(function(child) {
      if (child instanceof THREE.Mesh) {
        child.material = new THREE.MeshStandardMaterial({ color: Avatar.LArmC,});
      }
    });
    rightArm.traverse(function(child) {
      if (child instanceof THREE.Mesh) {
        child.material = new THREE.MeshStandardMaterial({ color: Avatar.RArmC,});
      }
    });
    HeadOBJ.traverse(function(child) {
      if (child instanceof THREE.Mesh) {
        child.material = new THREE.MeshStandardMaterial({ color: Avatar.HeadC, });
      }
    });
    Torso.traverse(function(child) {
      if (child instanceof THREE.Mesh) {
        child.material = new THREE.MeshStandardMaterial({ color: Avatar.TorsoC,});
      }
    });
  }
}
Canvas.onwheel = zoom;

setInterval(localCharPCode)

Canvas.onmousemove = function(e){
  if(Character.FP == true) {
    console.log("e1")
    controls.enabled = false
    camera.rotation.y -= e.movementX / 75
    if(camera.rotation.x - e.movementY / 75 < 1.55)     {
      if(camera.rotation.x - e.movementY / 75 > -1.55) {
        camera.rotation.x -= e.movementY / 75
      }
    }
    Body.rotation.y = camera.rotation.y
    Coneosle.log("e2")
  } else {
    console.log("notworking")
  }
}

// rendering function
function animate() {
  requestAnimationFrame(animate);
  if(Character.FP == false) {
    controls.update();
  }
  renderer.render(scene, camera);
};
Keys();
cc()
animate();


function loadMap(MapCode) {

}


// keys

document.body.addEventListener('keydown', function(e) {
  var key = e.code.replace('Key', '').toLowerCase();
  if (keyEe[key] !== undefined)
    keyEe[key] = true;
});
document.body.addEventListener('keyup', function(e) {
  var key = e.code.replace('Key', '').toLowerCase();
  if (keyEe[key] !== undefined)
    keyEe[key] = false;

});

async function jump() {
  let fi = 0
  for(let i=0; i < 17; i++) {
    fi += 1
    Body.position.y += 4 / fi / 1.8
    camera.position.y += 4 / fi / 1.8
    if(fi < 5) {
      Character.jumping = true
    } else {
      Character.jumping = false
    }
    await wait(0.01)
  }
}


function makeTextSprite(message, parameters) {
  if (parameters === undefined) parameters = {};
  var fontface = parameters.hasOwnProperty("fontface") ? parameters["fontface"] : "sans-serif";
  var fontsize = parameters.hasOwnProperty("fontsize") ? parameters["fontsize"] : 18;
  var borderThickness = parameters.hasOwnProperty("borderThickness") ? parameters["borderThickness"] : 1;
  var borderColor = parameters.hasOwnProperty("borderColor") ? parameters["borderColor"] : { r: 0, g: 0, b: 0, a: 1.0 };
  var backgroundColor = parameters.hasOwnProperty("backgroundColor") ? parameters["backgroundColor"] : { r: 0, g: 0, b: 255, a: 1.0 };
  var textColor = parameters.hasOwnProperty("textColor") ? parameters["textColor"] : { r: 0, g: 0, b: 0, a: 1.0 };

  var canvas = document.createElement('canvas');
  var context = canvas.getContext('2d');
  context.font = "Bold " + fontsize + "px " + fontface;
  var metrics = context.measureText(message);
  var textWidth = metrics.width;
  context.translate(canvas.width / 2 - 25, canvas.height / 2 - 25);

  context.fillStyle = "rgba(" + backgroundColor.r + "," + backgroundColor.g + "," + backgroundColor.b + "," + backgroundColor.a + ")";
  context.strokeStyle = "rgba(" + borderColor.r + "," + borderColor.g + "," + borderColor.b + "," + borderColor.a + ")";
  context.fillStyle = "rgba(" + textColor.r + ", " + textColor.g + ", " + textColor.b + ", 1.0)";
  context.fillText(message, borderThickness, fontsize + borderThickness);

  var texture = new THREE.Texture(canvas)
  texture.needsUpdate = true;
  var spriteMaterial = new THREE.SpriteMaterial({ map: texture, useScreenCoordinates: false });
  var sprite = new THREE.Sprite(spriteMaterial);
  sprite.scale.set(0.5 * fontsize, 0.25 * fontsize, 0.75 * fontsize);
  return sprite;
}

$(function() {
  socket.on('plr', function(plrid) {
    const plr = idV.clone()
    plr.position.y = 1
    scene.add(plr)
    function PlayerONL() {
      requestAnimationFrame(PlayerONL)
      if (Character.Player == plrid) {
        socket.emit('GETPOS', Body.position.z, Body.position.y, Body.position.x, Character.Player)
      }
      socket.on('PosGiven', function(Z, Y, X) {
          plr.position.z = Z
          plr.position.y = Y
          plr.position.x = X
      })
    }
    PlayerONL()
  })
})
