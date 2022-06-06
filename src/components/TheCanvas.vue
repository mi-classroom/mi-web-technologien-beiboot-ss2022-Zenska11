<template>
  <div id="scene-container">
    
  </div>
</template>


<script>
import * as THREE from 'three'
import { PointerLockControls } from "three/examples/jsm/controls/PointerLockControls";
import {Text} from 'troika-three-text';
import json from '../../cda-paintings.json';
import image from '../assets/images/gras.jpg'
import image2 from '../assets/images/stoff.jpg'

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
      moveForward: false,
      moveBackward: false,
      moveLeft: false,
      moveRight: false,
      image: image,
      image2: image2,
      
    }
  },
  // ToDos:
  // - Date aus dem Bildern entfernen
  // - Größe der Bilder implementieren
  methods: {
    init: function() {
      this.scene = new THREE.Scene()
      this.scene.background = new THREE.Color( 0x40a6ff );
      this.scene.fog = new THREE.FogExp2( 0x40a6ff, 0.01 );
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
      

      this.renderer = new THREE.WebGLRenderer()
      this.renderer.setSize(window.innerWidth -10, window.innerHeight - 20)
      document.getElementById("scene-container").appendChild(this.renderer.domElement);


      //needed variables
      let url ="";
      let newUrl = "";
      let ranking = "";
      let timeLine = 1500;

      let title = "";
      let date = "";
      let involvedPerson = "";
      let medium = "";
      let repository = "";

      let zCoordinate = 0;
      let xCoordinate = 0;

      // for-Schleife, die alle Paintings erstellt 
      this.paintings.items.forEach(element => {

        // alte url durch neue url ersetzen
        url = element.images.overall.images[0].sizes.medium.src;
        newUrl = url.replace( "imageserver-2022", "data-proxy/image.php?subpath=")
        
        ranking = element.sortingInfo.year;
      
        // Timeline Text
        const myText1 = new Text()
        this.scene.add(myText1)
        // Set properties to configure:
        myText1.text = timeLine
        myText1.fontSize = 1
        myText1.position.z = -0.1 + zCoordinate
        myText1.position.y = 2
        myText1.position.x = -10
        myText1.color = 0xfa842b
        
        if (ranking === timeLine) {
          

          // frontside Plane
          const frontPictureGeometry = new THREE.PlaneGeometry(5, 5)
          const imageTexture = new THREE.TextureLoader().load( newUrl );
          const imagematerial = new THREE.MeshBasicMaterial( { map: imageTexture } );
          this.frontPicture = new THREE.Mesh(frontPictureGeometry, imagematerial)
          
          this.frontPicture.position.y = 3
          this.frontPicture.position.z = zCoordinate
  
          this.scene.add(this.frontPicture)
  
          
          // Backside Plane
          const backsideGeometry = new THREE.PlaneGeometry(5, 5)
          const imagebackTexture = new THREE.TextureLoader().load( this.image2 );
          imagebackTexture.wrapS = THREE.RepeatWrapping;
          imagebackTexture.wrapT = THREE.RepeatWrapping;
          imagebackTexture.repeat.set(3, 3);
          const TextMaterial = new THREE.MeshBasicMaterial( {map: imagebackTexture } );
          this.backsiteText = new THREE.Mesh(backsideGeometry, TextMaterial)
          
          this.backsiteText.position.y = 3
          this.backsiteText.rotation.y = 3.15
          this.backsiteText.position.z = zCoordinate
          this.backsiteText.position.x = xCoordinate
          
          this.scene.add(this.backsiteText)
  
          title = element.metadata.title;
          date = element.sortingInfo.year;
          involvedPerson = element.involvedPersons[0].name;
          medium = this.removeBrackets(element.medium); 
          repository = element.repository;
          //Text
          const myText = new Text()
          this.scene.add(myText)
          // Set properties to configure:
          myText.text = 'Titel: ' +title + '\n' + 'Künstler: ' + involvedPerson + '\n' + 'Datierung: ' + date + '\n' + 'Art des Werks: ' + medium + '\n' + 'Besitzer: ' + repository
          myText.fontSize = 0.1
          myText.position.z = -0.1 + zCoordinate
          myText.position.y = 2
          myText.position.x = 1.8 + xCoordinate
          myText.rotation.y = 3.15
          myText.color = 0x000000

          this.frontPicture.position.x = xCoordinate
          xCoordinate = xCoordinate +10;
          return
        }

        xCoordinate = 0;
        zCoordinate = zCoordinate -7;
        timeLine = ranking;
      });

      // Floor
      const floorGeometry = new THREE.PlaneGeometry( 2000, 2000, 100, 100 );
      const imageTexture = new THREE.TextureLoader().load( this.image );
      imageTexture.wrapS = THREE.RepeatWrapping;
      imageTexture.wrapT = THREE.RepeatWrapping;
      imageTexture.repeat.set(500, 500);
      
			const floorMaterial = new THREE.MeshBasicMaterial( { map: imageTexture } );
			this.floor = new THREE.Mesh( floorGeometry, floorMaterial );
      this.scene.add(this.floor);
      

      this.floor.rotation.x = -1.57  

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