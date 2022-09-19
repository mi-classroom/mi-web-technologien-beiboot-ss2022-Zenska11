<template>
  <div id="scene-container"></div>
</template>

<script>
import * as THREE from "three";
import { PointerLockControls } from "three/examples/jsm/controls/PointerLockControls";
import { Text } from "troika-three-text";
// images reinladen
import json from "../../cda-paintings.json";
import image from "../assets/images/gras.jpg";
import image2 from "../assets/images/stoff.jpg";

export default {
  name: "ThreeTest",
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
      moveUp_: false,
      moveDown: false,
      moveLeft: false,
      moveRight: false,
      minY: 1.8,
      image: image,
      image2: image2,
      projector: null,
      mouse: null,
      raycaster: null,
      vector: null,
    };
  },

  methods: {
    init: function () {
      // scene erstellen
      this.scene = new THREE.Scene();
      this.scene.background = new THREE.Color(0x40a6ff);
      this.scene.fog = new THREE.FogExp2(0x40a6ff, 0.005);
      // camera erstellen
      this.camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        1,
        1000
      );
      this.camera.position.z = 10;
      this.camera.position.y = 1.8;
      // raycaster erstellen
      this.raycaster = new THREE.Raycaster();
      this.mouse = new THREE.Vector2();

      // controls
      this.velocity = new THREE.Vector3();
      this.direction = new THREE.Vector3();
      this.clock = new THREE.Clock();
      this.vector = new THREE.Vector3();

      this.controls = new PointerLockControls(this.camera, document.body);

      document.body.addEventListener(
        "click",
        (event) => {
          // Left click
          if (event.button === 0) {
            if (!this.controls.isLocked) {
              this.controls.lock();
            }
          }
        },
        false
      );

      this.scene.add(this.controls.getObject());

      const onKeyDown = (event) => {
        switch (event.code) {
          case "ArrowUp":
          case "KeyW":
            this.moveForward = true;
            break;

          case "ArrowLeft":
          case "KeyA":
            this.moveLeft = true;
            break;

          case "ArrowDown":
          case "KeyS":
            this.moveBackward = true;
            break;

          case "ArrowRight":
          case "KeyD":
            this.moveRight = true;
            break;
          case "Space":
            this.moveUp_ = true;
            break;
          case "ShiftLeft":
          case "ShiftRight":
            this.moveDown = true;
            break;
        }
      };

      const onKeyUp = (event) => {
        switch (event.code) {
          case "ArrowUp":
          case "KeyW":
            this.moveForward = false;
            break;

          case "ArrowLeft":
          case "KeyA":
            this.moveLeft = false;
            break;

          case "ArrowDown":
          case "KeyS":
            this.moveBackward = false;
            break;

          case "ArrowRight":
          case "KeyD":
            this.moveRight = false;
            break;
          case "Space":
            this.moveUp_ = false;
            break;
          case "ShiftLeft":
          case "ShiftRight":
            this.moveDown = false;
            break;
        }
      };

      document.addEventListener("keydown", onKeyDown);
      document.addEventListener("keyup", onKeyUp);

      this.renderer = new THREE.WebGLRenderer();
      this.renderer.setSize(window.innerWidth - 10, window.innerHeight - 20);
      document
        .getElementById("scene-container")
        .appendChild(this.renderer.domElement);

      // this.projector = new THREE.Projector();
      // this.mouseVector = new THREE.Vector3();

      document.addEventListener("click", this.onMouseClick, false);

      //needed variables
      let url = "";
      let newUrl = "";
      let refUrl = "";
      let newRefUrl = "";
      let ranking = "";
      let timeLine = 1500;

      let title = "";
      let involvedPerson = "";
      let medium = "";
      let repository = "";

      let zCoordinate = 0;
      let xCoordinate = 0;
      let yCoordinate = 0;
      let yRefCoordinate = 0;

      let ratio = 0;
      let refRatio = 0;
      let pictureHeight = 0;
      let pictureScalingFactor = 10;

      // calculate height for the plane
      const calculateHeight = (element) => {
        const split = element.dimensions.replace(/[\])}[{(]/g, " ").split(" ");
        const scalingFactor = 1 / 1.8;
        const splitWithoutCM = split.filter(
          (string) => string !== "cm" && string !== ""
        );

        let size;
        let sideMeasured;

        for (const string of splitWithoutCM) {
          const stringSlicedAtDash = string.split("-")[0];

          if (!size) {
            if (/\d/.test(stringSlicedAtDash)) {
              size = parseFloat(stringSlicedAtDash.replace(/,/g, "."));
            }
          } else {
            sideMeasured = stringSlicedAtDash;

            break;
          }
        }

        // für die Kreise
        switch (sideMeasured) {
          case "oben":
            size =
              (size /
                element.images.overall.images[0].sizes.medium.dimensions
                  .width) *
              element.images.overall.images[0].sizes.medium.dimensions.height;

            break;
          case "Durchmesser":
            /* eslint-disable */
            const scaledDiameter = Math.sqrt(
              Math.pow(
                element.images.overall.images[0].sizes.medium.dimensions.width,
                2
              ) +
                Math.pow(
                  element.images.overall.images[0].sizes.medium.dimensions
                    .height,
                  2
                )
            );

            const scalingFactor = size / scaledDiameter;

            size =
              element.images.overall.images[0].sizes.medium.dimensions.height *
              scalingFactor;

            break;
          default:
            break;
        }

        return (size / 100) * scalingFactor;
      };

      // Funktion, welche alle Gemälde mit Vorderseite, Rückseite und Text erstellt und ausgibt
      const renderedPainting = (painting) => {
        // frontside Plane
        const frontPictureGeometry = new THREE.PlaneGeometry(
          (pictureHeight / ratio) * pictureScalingFactor,
          pictureHeight * pictureScalingFactor
        );
        const imageTexture = new THREE.TextureLoader().load(newUrl);
        const imagematerial = new THREE.MeshBasicMaterial({
          map: imageTexture,
        });
        this.frontPicture = new THREE.Mesh(frontPictureGeometry, imagematerial);
        this.frontPicture.userData.id = painting.inventoryNumber;

        this.frontPicture.position.y = (pictureHeight / 2) * 10 + 1;
        this.frontPicture.position.z = zCoordinate;

        this.scene.add(this.frontPicture);
        this.frontPicture.position.x = xCoordinate;

        // Backside Plane
        const backsideGeometry = new THREE.PlaneGeometry(
          (pictureHeight / ratio) * pictureScalingFactor,
          pictureHeight * pictureScalingFactor
        );
        const imagebackTexture = new THREE.TextureLoader().load(this.image2);
        imagebackTexture.wrapS = THREE.RepeatWrapping;
        imagebackTexture.wrapT = THREE.RepeatWrapping;
        imagebackTexture.repeat.set(3, 3);
        const TextMaterial = new THREE.MeshBasicMaterial({
          map: imagebackTexture,
        });
        this.backsiteText = new THREE.Mesh(backsideGeometry, TextMaterial);

        this.backsiteText.position.y = (pictureHeight / 2) * 10 + 1;
        this.backsiteText.rotation.y = 3.15;
        this.backsiteText.position.z = zCoordinate;
        this.backsiteText.position.x = xCoordinate;

        this.scene.add(this.backsiteText);

        title = painting.metadata.title;
        involvedPerson = painting.involvedPersons[0].name;
        medium = this.removeBrackets(painting.medium);
        repository = painting.repository;
        //Text
        const myText = new Text();
        this.scene.add(myText);
        // Set properties to configure:
        myText.text =
          "Titel: " +
          title +
          "\n" +
          "Künstler: " +
          involvedPerson +
          "\n" +
          "Art des Werks: " +
          medium +
          "\n" +
          "Besitzer: " +
          repository;
        myText.fontSize = 0.1;
        myText.position.z = -0.1 + zCoordinate;
        myText.position.y = 2;
        myText.position.x = 1 + xCoordinate;
        myText.rotation.y = 3.15;
        myText.color = 0x000000;
      };

      // Funktion, welche für jedes Gemälde die Referenzgemälde auf der Rückseite der Hauptbilder erstellt und ausgibt
      const renderedReferences = (painting) => {
        // Loop durch die Referenzen eines Bildes
        painting.references.forEach((reference) => {
          // sucht in dem gesamten Painting nach dem Referenz Painting
          this.paintings.items.forEach((element) => {
            if (element.metadata.id == reference.inventoryNumber) {
              // alte url durch neue url ersetzen
              refUrl = element.images.overall.images[0].sizes.medium.src;
              newRefUrl = refUrl.replace(
                "imageserver-2022",
                "data-proxy/image.php?subpath="
              );
              // ratio für ref Bilder berechnen
              refRatio =
                element.images.overall.images[0].sizes.medium.dimensions
                  .height /
                element.images.overall.images[0].sizes.medium.dimensions.width;

              // Reference Plane
              const backsideGeometry = new THREE.PlaneGeometry(
                ((pictureHeight / refRatio) * pictureScalingFactor) / 3,
                (pictureHeight * pictureScalingFactor) / 3
              );
              const imagebackTexture = new THREE.TextureLoader().load(
                newRefUrl
              );
              const TextMaterial = new THREE.MeshBasicMaterial({
                map: imagebackTexture,
              });

              this.backsiteText = new THREE.Mesh(
                backsideGeometry,
                TextMaterial
              );
              this.backsiteText.userData.id = element.inventoryNumber;

              this.backsiteText.position.y = (pictureHeight / 2) * 10 + 1;
              this.backsiteText.rotation.y = 3.15;
              this.backsiteText.position.z = zCoordinate - 0.1;
              this.backsiteText.position.x = xCoordinate;
              this.backsiteText.position.y = yRefCoordinate;

              this.scene.add(this.backsiteText);

              // jedes Referenzbild wird über das vorherige Referenzbild gesetzt
              yRefCoordinate += (pictureHeight / 2) * 10;
            }
          });
        });
      };

      // Auffruf der Golffahne
      this.golfflag();

      // for-Schleife, die alle Paintings erstellt
      this.paintings.items.forEach((element) => {
        // alte url durch neue url ersetzen
        url = element.images.overall.images[0].sizes.medium.src;
        newUrl = url.replace(
          "imageserver-2022",
          "data-proxy/image.php?subpath="
        );

        ranking = element.sortingInfo.year;

        // ratio berechnen
        ratio =
          element.images.overall.images[0].sizes.medium.dimensions.height /
          element.images.overall.images[0].sizes.medium.dimensions.width;

        // Timeline Text
        const myText1 = new Text();
        this.scene.add(myText1);
        myText1.text = timeLine;
        myText1.fontSize = 1;
        myText1.position.z = -0.1 + zCoordinate;
        myText1.position.y = 2;
        myText1.position.x = -10;
        myText1.color = 0xfa842b;

        pictureHeight = calculateHeight(element);
        yCoordinate = (pictureHeight / 2) * 10 + 1;

        // if true -> wird ein Gemälde in die nächste Reihe gepackt, false -> Bild ist aus dem selben Jahr und kommt daneben.
        if (ranking != timeLine) {
          xCoordinate =
            ((pictureHeight / ratio) * pictureScalingFactor) / 2 - 2;
          //xRefCoordinate = xCoordinate;
          yRefCoordinate = yCoordinate;
          zCoordinate = zCoordinate - 7;
          timeLine = ranking;

          // Bild rendern
          renderedPainting(element);
          // Referenzbilder einfügen
          renderedReferences(element);
          return;
        }

        xCoordinate =
          xCoordinate + 6 + (pictureHeight / ratio) * pictureScalingFactor;
        //xRefCoordinate = xCoordinate;
        yRefCoordinate = yCoordinate;

        // Bild rendern
        renderedPainting(element);
        // Referenzbilder einfügen
        renderedReferences(element);

        timeLine = ranking;
      });

      // Floor
      const floorGeometry = new THREE.PlaneGeometry(2000, 2000, 100, 100);
      const imageTexture = new THREE.TextureLoader().load(this.image);
      imageTexture.wrapS = THREE.RepeatWrapping;
      imageTexture.wrapT = THREE.RepeatWrapping;
      imageTexture.repeat.set(500, 500);

      const floorMaterial = new THREE.MeshBasicMaterial({ map: imageTexture });
      this.floor = new THREE.Mesh(floorGeometry, floorMaterial);
      this.scene.add(this.floor);

      this.floor.rotation.x = -1.57;
    },
    animate: function () {
      requestAnimationFrame(this.animate);

      if (this.controls.isLocked === true) {
        const delta = this.clock.getDelta();

        this.velocity.x -= this.velocity.x * 10.0 * delta;
        this.velocity.z -= this.velocity.z * 10.0 * delta;
        this.velocity.y -= this.velocity.y * 10.0 * delta;

        this.direction.z = Number(this.moveForward) - Number(this.moveBackward);
        this.direction.x = Number(this.moveRight) - Number(this.moveLeft);
        this.direction.y = Number(this.moveUp_) - Number(this.moveDown);
        this.direction.normalize(); // this ensures consistent movements in all directions

        if (this.moveForward || this.moveBackward)
          this.velocity.z -= this.direction.z * 400.0 * delta;
        if (this.moveLeft || this.moveRight)
          this.velocity.x -= this.direction.x * 400.0 * delta;
        if (this.moveUp_ || this.moveDown)
          this.velocity.y -= this.direction.y * 400 * delta;

        this.controls.moveRight(-this.velocity.x * delta);
        this.controls.moveForward(-this.velocity.z * delta);
        this.moveUp(-this.velocity.y * delta);

        if (this.controls.getObject().position.y < this.minY) {
          this.velocity.y = 0;
          this.controls.getObject().position.y = this.minY;
        }
      } else {
        this.velocity.set(0, 0, 0);
      }

      //this.selectPiece();
      this.renderer.render(this.scene, this.camera);
    },
    moveUp(distance) {
      this.vector.setFromMatrixColumn(this.camera.matrix, 0);
      this.vector.crossVectors(this.camera.up, this.vector);
      this.camera.position.addScaledVector(this.camera.up, distance);
    },
    removeBrackets(text) {
      let roundBracketsRemoved = text.split("(")[0];
      let squareBracketsRemoved = roundBracketsRemoved.split("[")[0];
      return squareBracketsRemoved;
    },
    // wählt ein Gemälde aus
    onMouseClick(e) {
      if (this.controls.isLocked === true) {
        this.selectPiece();
      }
    },
    // wählt ein Gemälde aus und öffnet den dazugehörigen Link in einem neuen Tab
    selectPiece() {
      this.raycaster.setFromCamera(this.mouse, this.camera);
      const intersects = this.raycaster.intersectObjects(this.scene.children);
      if (intersects.length > 0 && intersects[0].distance < 5) {
        window.open(
          "https://lucascranach.org/de/" + intersects[0].object.userData.id,
          "_blank"
        );
      }
    },
    // erstellt die beiden Golffahnen
    golfflag() {
      // flag 1
      const geometry = new THREE.CylinderGeometry(0.02, 0.02, 10, 32);
      const material = new THREE.MeshBasicMaterial({ color: 0xc7c7c7 });
      const cylinder = new THREE.Mesh(geometry, material);
      cylinder.position.x = -7;
      this.scene.add(cylinder);

      const geometry1 = new THREE.BoxGeometry(1.5, 1, 0.01);
      const material1 = new THREE.MeshBasicMaterial({ color: 0x971a11 });
      const cube = new THREE.Mesh(geometry1, material1);
      cube.position.y = 4.5;
      cube.position.x = -6.25;
      this.scene.add(cube);

      // flag 2
      const geometry3 = new THREE.CylinderGeometry(0.02, 0.02, 10, 32);
      const material3 = new THREE.MeshBasicMaterial({ color: 0xc7c7c7 });
      const cylinder2 = new THREE.Mesh(geometry3, material3);
      cylinder2.position.x = -7;
      cylinder2.position.z = -280;
      this.scene.add(cylinder2);

      const geometry4 = new THREE.BoxGeometry(1.5, 1, 0.01);
      const material4 = new THREE.MeshBasicMaterial({ color: 0x971a11 });
      const cube2 = new THREE.Mesh(geometry4, material4);
      cube2.position.y = 4.5;
      cube2.position.x = -6.25;
      cube2.position.z = -280;
      this.scene.add(cube2);
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
    this.paintings.items = this.paintings.items.filter((item) => item.isBestOf);

    this.init();
    this.animate();
  },
};
</script>
