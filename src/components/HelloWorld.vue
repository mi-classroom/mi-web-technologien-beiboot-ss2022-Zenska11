<template>
  <div>  
    <ul v-for="painting in paintings.items" :key="painting.objectId">
        <li v-if="painting.isBestOf == true">
          <p v-if="painting.images.overall != undefined"><img :src="painting.images.overall.images[0].sizes.xsmall.src"></p>
          <p>Titel: {{painting.metadata.title}}</p>
          <p>Datierung: {{painting.metadata.date}}</p>
          <p>Art des Werks: {{removeBrackets(painting.medium)}}</p>
          <p>Besitzer: {{painting.repository}}</p>
          <br>
        </li>
      </ul>
</div>
  
</template>

<script>
import json from '../../cda-paintings.json';

/* eslint-disable */
export default{
    data(){
        return{
            paintings: json,
            splitedStr: "",   
            images: null      
        };
    },
    mounted(){
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
      },
    methods: {
      removeBrackets(text) {
        let roundBracketsRemoved = text.split("(")[0];
	      let squareBracketsRemoved = roundBracketsRemoved.split("[")[0];
        return squareBracketsRemoved;
      },
    }
}
</script>

