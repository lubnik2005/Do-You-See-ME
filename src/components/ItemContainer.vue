<template>
    <div style="position:absolute"
        @drop='onDrop($event, currentId)' 
        @dragover.prevent
        @dragenter.prevent
    
    >
        <img class="container-image wobble" :src="require('@/assets/images/sheep.png')" />
        <div class="item-container ">
            <item v-for="item in items.filter(item => item.list == currentId)" :item="item" :key="'item-' + item.id" />
        </div>
    </div>
</template>
<script>
import Item from './Item';
export default {
    name: 'ItemContainer',
    methods: {
        onDrop: function(event,list){
            console.log({event:event,list:list});
            this.$emit('onDrop', {event:event,list:list})
        }
    },
    created: function(){
        console.log(this.items);
    },
    props: [
        'items','currentId'
    ],
    components: {
        Item
    }
}
</script>

<style scoped lang="scss">
  .wobble {
    animation: rotation 3s infinite linear;
    transform-style: preserve-3d;
    transform: perspective(500px);
  }
  
  $rotation: 5deg;
  @keyframes rotation {
    0% {
      transform: rotate($rotation);
    }
    50% {
      transform: rotate(-$rotation);
    }
    100% {
        transform: rotate($rotation);
    }
  }
  
</style>