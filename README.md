<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>MiniCraft</title>
  <style>
    body { margin: 0; overflow: hidden; }
    canvas { display: block; }
  </style>
</head>
<body>
  <script type="module">
    import * as THREE from 'https://cdn.skypack.dev/three@0.152.2';
    import { PointerLockControls } from 'https://cdn.skypack.dev/three/examples/jsm/controls/PointerLockControls.js';

    // Scene setup
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(75, window.innerWidth/window.innerHeight, 0.1, 1000);
    const renderer = new THREE.WebGLRenderer();
    renderer.setSize(window.innerWidth, window.innerHeight);
    document.body.appendChild(renderer.domElement);

    // Lighting
    const light = new THREE.DirectionalLight(0xffffff, 1);
    light.position.set(5, 10, 7.5);
    scene.add(light);

    // Controls
    const controls = new PointerLockControls(camera, document.body);
    document.body.addEventListener('click', () => controls.lock());
    scene.add(controls.getObject());

    // Ground
    const groundSize = 20;
    for (let x = -groundSize; x < groundSize; x++) {
      for (let z = -groundSize; z < groundSize; z++) {
        addBlock(x, 0, z);
      }
    }
    // Add/Remove Blocks
    const blockGeometry = new THREE.BoxGeometry(1, 1, 1);
    const blockMaterial = new THREE.MeshStandardMaterial({ color: 0x8B4513 });
    const blocks = [];

    function addBlock(x, y, z) {
      const block = new THREE.Mesh(blockGeometry, blockMaterial.clone());
      block.position.set(x, y, z);
      scene.add(block);
      blocks.push(block);
    }

    // Raycasting for block interaction
    const raycaster = new THREE.Raycaster();
    const mouse = new THREE.Vector2();

    window.addEventListener('mousedown', () => {
      raycaster.setFromCamera(new THREE.Vector2(), camera);
      const intersects = raycaster.intersectObjects(blocks);
      if (intersects.length > 0) {
        const hit = intersects[0];
        if (event.button === 0) {
          // Left click = remove block
          scene.remove(hit.object);
          blocks.splice(blocks.indexOf(hit.object), 1);
        } else if (event.button === 2) {
          // Right click = place block
          const pos = hit.object.position.clone().add(hit.face.normal);
          addBlock(Math.round(pos.x), Math.round(pos.y), Math.round(pos.z));
        }
      }
    });

    window.addEventListener('contextmenu', e => e.preventDefault());

    // Movement
    const move = { forward: false, backward: false, left: false, right: false };
    document.addEventListener('keydown', e => {
      if (e.code === 'KeyW') move.forward = true;
      if (e.code === 'KeyS') move.backward = true;
      if (e.code === 'KeyA') move.left = true;
      if (e.code === 'KeyD') move.right = true;
    });
    document.addEventListener('keyup', e => {
      if (e.code === 'KeyW') move.forward = false;
      if (e.code === 'KeyS') move.backward = false;
      if (e.code === 'KeyA') move.left = false;
      if (e.code === 'KeyD') move.right = false;
    });

    const velocity = new THREE.Vector3();
    const speed = 0.1;

    function animate() {
      requestAnimationFrame(animate);

      if (controls.isLocked) {
        velocity.set(0, 0, 0);
        if (move.forward) velocity.z -= speed;
        if (move.backward) velocity.z += speed;
        if (move.left) velocity.x -= speed;
        if (move.right) velocity.x += speed;

        controls.moveRight(velocity.x);
        controls.moveForward(velocity.z);
      }

      renderer.render(scene, camera);
    }

    camera.position.y = 2;
    animate();
  </script>
</body>
</html>












 
# my-own-minecraft







