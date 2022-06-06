<!-- <template>
  <Renderer ref="rendererC" antialias :orbit-ctrl="{ enableDamping: true }" resize="window">
    <Camera :position="{ z: 10 }" />
    <Scene>
      <PointLight :position="{ y: 50, z: 50 }" />
      <Box :size="1" ref="meshC" :rotation="{ y: Math.PI / 4, z: Math.PI / 4 }">
        <LambertMaterial />
      </Box>
    </Scene>
  </Renderer>
</template> -->


<template>
  <div id="scene-container">
    
  </div>
</template>


<script>
import * as THREE from 'three'
import { PointerLockControls } from "three/examples/jsm/controls/PointerLockControls";
import {Text} from 'troika-three-text';
import json from '../../cda-paintings.json';

export default {
  name: 'ThreeTest',
  data() {
    return {
      paintings: json,
      cube: null,
      renderer: null,
      scene: null,
      camera: null,
      frontPicture: null,
      backsiteText: null,
      controls: null,
      clock: null,
      floor: null,
      velocity: null,
      direction: null,
      vertex: null,
      moveForward: false,
      moveBackward: false,
      moveLeft: false,
      moveRight: false,
      raycaster: null,
      objects: null,
      
    }
  },
  
  methods: {
    init: function() {
      this.scene = new THREE.Scene()
      this.camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        1,
        1000
      )
      this.camera.position.z = 10
      this.camera.position.y = 1.8

      // controls
      this.velocity = new THREE.Vector3();
      this.direction = new THREE.Vector3();
      this.clock = new THREE.Clock();


      this.controls = new PointerLockControls( this.camera, document.body );

      document.body.addEventListener("click", (event) => {
            // Left click 
            if(event.button === 0) {
                if(!this.controls.isLocked) {
                    this.controls.lock();
                }
            }
        }, false);
      

      
      this.scene.add( this.controls.getObject() );

      const onKeyDown = ( event ) => {

					switch ( event.code ) {

						case 'ArrowUp':
						case 'KeyW':
							this.moveForward = true;
							break;

						case 'ArrowLeft':
						case 'KeyA':
							this.moveLeft = true;
							break;

						case 'ArrowDown':
						case 'KeyS':
							this.moveBackward = true;
							break;

						case 'ArrowRight':
						case 'KeyD':
							this.moveRight = true;
							break;

					}

				};

				const onKeyUp = ( event ) => {

					switch ( event.code ) {

						case 'ArrowUp':
						case 'KeyW':
							this.moveForward = false;
							break;

						case 'ArrowLeft':
						case 'KeyA':
							this.moveLeft = false;
							break;

						case 'ArrowDown':
						case 'KeyS':
							this.moveBackward = false;
							break;

						case 'ArrowRight':
						case 'KeyD':
							this.moveRight = false;
							break;

					}

				};

        document.addEventListener( 'keydown', onKeyDown );
				document.addEventListener( 'keyup', onKeyUp );

       // this.raycaster = new THREE.Raycaster( new THREE.Vector3(), new THREE.Vector3( 0, - 1, 0 ), 0, 10 );

      

      this.renderer = new THREE.WebGLRenderer()
      this.renderer.setSize(window.innerWidth, window.innerHeight)
      document.body.appendChild(this.renderer.domElement)



      // // TEST cube
      // const boxGeometry = new THREE.BoxGeometry(2, 2, 2)
      // const material = new THREE.MeshBasicMaterial({ color: 0x00ffff })
      // this.cube = new THREE.Mesh(boxGeometry, material)
      // this.scene.add(this.cube)

      //needed variables
      let n = 0
      let url ="";
      let newUrl = "";

      let title = "";
      let involvedPerson = "";
      let medium = "";
      let repository = "";

      let zCoordinate = 0;
      this.paintings.items.forEach(element => {
        console.log(n);
        n++;
        //console.log(element.images.overall.images[0].sizes.medium.src);
        url = element.images.overall.images[0].sizes.medium.src;
        console.log(url)
        newUrl = url.replace( "imageserver-2022", "data-proxy/image.php?subpath=")
        console.log(newUrl)

      // frontside Plane
      const frontPictureGeometry = new THREE.PlaneGeometry(5, 5)
      const imageTexture = new THREE.TextureLoader().load( newUrl );
      const imagematerial = new THREE.MeshBasicMaterial( { map: imageTexture } );
      this.frontPicture = new THREE.Mesh(frontPictureGeometry, imagematerial)
      
      this.frontPicture.position.y = 1.8
      this.frontPicture.position.z = zCoordinate

      this.scene.add(this.frontPicture)

      
      // Backside Plane
      const backsideGeometry = new THREE.PlaneGeometry(5, 5)
      const TextMaterial = new THREE.MeshBasicMaterial( {color: 0xffffff } );
      this.backsiteText = new THREE.Mesh(backsideGeometry, TextMaterial)
      
      this.backsiteText.position.y = 1.9
      this.backsiteText.rotation.y = 3.15
      this.backsiteText.position.z = zCoordinate
      
      this.scene.add(this.backsiteText)

      title = element.metadata.title;
      
      involvedPerson = element.involvedPersons[0].name;
      medium = this.removeBrackets(element.medium); 
      repository = element.repository;
      //Text
      const myText = new Text()
      this.scene.add(myText)
      // Set properties to configure:
      myText.text = 'Titel: ' +title + '\n' + 'Künstler: ' + involvedPerson + '\n' + 'Art des Werks: ' + medium + '\n' + 'Besitzer: ' + repository
      myText.fontSize = 0.1
      myText.position.z = -0.1 + zCoordinate
      myText.position.y = 2
      myText.position.x = 1.8
      myText.rotation.y = 3.15
      myText.color = 0x9966FF




      zCoordinate = zCoordinate -5;

      });




      
      // // Frontside Plane
      // const frontPictureGeometry = new THREE.PlaneGeometry(5, 5)
      // const imageTexture = new THREE.TextureLoader().load( 'https://lucascranach.org/data-proxy/image.php?subpath=/AT_KHM_GG6905_FR001/01_Overall/AT_KHM_GG6905_FR001_2008-08_Overall-m.jpg' );
      // const imagematerial = new THREE.MeshBasicMaterial( { map: imageTexture } );
      // this.frontPicture = new THREE.Mesh(frontPictureGeometry, imagematerial)
      // this.scene.add(this.frontPicture)

      // // Backside Plane
      // const backsideGeometry = new THREE.PlaneGeometry(5, 5)
      // const textTexture = new THREE.TextureLoader().load( 'https://lucascranach.org/data-proxy/image.php?subpath=/CH_SORW_1925-1b_FR006/01_Overall/CH_SORW_1925-1b_FR006_2008-11_Overall-s.jpg' );
      // const TextMaterial = new THREE.MeshBasicMaterial( { map: textTexture } );
      // this.backsiteText = new THREE.Mesh(backsideGeometry, TextMaterial)
      // this.scene.add(this.backsiteText)
      //Text
      // const myText = new Text()
      // this.scene.add(myText)
      // // Set properties to configure:
      // myText.text = ' Hallo'
      // myText.fontSize = 0.1
      // myText.position.z = -0.1
      // myText.position.y = 1.8
      // myText.rotation.y = 3
      // myText.color = 0x9966FF

      // Floor
      const floorGeometry = new THREE.PlaneGeometry( 2000, 2000, 100, 100 );
			const floorMaterial = new THREE.MeshBasicMaterial( { color: 0xf7f9ef } );
			this.floor = new THREE.Mesh( floorGeometry, floorMaterial );
      this.scene.add(this.floor);
      

      this.floor.rotation.x = -1.57
      //this.floor.rotation.x = (- Math.PI / 2)
      // this.cube.position.y = 1.8
      // this.cube.position.z = - 2
      //this.frontPicture.position.y = 1.9
      //this.backsiteText.position.y = 1.9
      //this.backsiteText.rotateY(3)

      //this.backsiteText.position.z = 1.9
  
      



    },
    animate: function() {
      requestAnimationFrame(this.animate)

      // this.cube.rotation.x += 0.01
      // this.cube.rotation.y += 0.01

      if ( this.controls.isLocked === true ) {

					

					const delta = this.clock.getDelta();

					this.velocity.x -= this.velocity.x * 10.0 * delta;
					this.velocity.z -= this.velocity.z * 10.0 * delta;

					this.direction.z = Number( this.moveForward ) - Number( this.moveBackward );
					this.direction.x = Number( this.moveRight ) - Number( this.moveLeft );
					this.direction.normalize(); // this ensures consistent movements in all directions

					if ( this.moveForward || this.moveBackward )this. velocity.z -= this.direction.z * 400.0 * delta;
					if ( this.moveLeft || this.moveRight ) this.velocity.x -= this.direction.x * 400.0 * delta;

				

					this.controls.moveRight( - this.velocity.x * delta );
					this.controls.moveForward( - this.velocity.z * delta );


				}



      this.renderer.render(this.scene, this.camera)
    },
    /* eslint-disable */
    removeBrackets(text) {
        let roundBracketsRemoved = text.split("(")[0];
	      let squareBracketsRemoved = roundBracketsRemoved.split("[")[0];
        return squareBracketsRemoved;
      },
  },
  mounted() {
    // sortiert die Bilder nach dem Erscheinungsjahr
    this.paintings.items.sort((a, b) => {
      let fa = a.sortingNumber.toLowerCase(),
          fb = b.sortingNumber.toLowerCase();

      if (fa < fb) {
          return -1;
      }
      if (fa > fb) {
          return 1;
      }
      return 0;
    });

    // Nimmt nur die Bilder in das Array auf, wo das Feld "isBestOf" true ist
    this.paintings.items = this.paintings.items.filter(item => item.isBestOf);

    this.init()
    this.animate()
  }
}
</script>