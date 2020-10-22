<template>
  <div id="app">
    <div>
    <h4 v-text="appName" align="center" />

      <div id="gameboard-container" class="nes-container with-title is-centered">
        <img
          :src="require('@/assets/images/green_plain.jpg')"
          style="position: absolute; top: 0px; left: 0px; z-index: -29"
        />
        <div v-for="(wall,index) in walls" :key="index" :style="wall" class="wall" />
        <item-container
          v-for="item in items"
          :items="items"
          :currentId="item.id"
          :key="'item-container-'+item.id"
          :style="item.location"
          @onDrop="onDrop"
        />
      </div>
    </div>

      <div id="sidebar" class="nes-container with-title is-centered">
        <p class="title" v-text="sideBarName" />
        <div class="nes-container" style="height: 300px;"  align="left" >
          <p v-text="text" />
          <ul>
            <li v-for="(clue,index) in clues" :key="index" v-text="clue" />
          </ul>
        </div>
        <div class="nes-container" style="height: 150px;overflow-y: scroll;" >
          <item
            v-for="item in items.filter(item => item.list == 0)"
            :key="'item-' + item.id"
            :item="item"
            @emittingDrag="startDrag"
          />
        </div>
        <div class="nes-container" style="height:300px" >
              <audio id="dropAudio">
                <source src="./assets/audio/drop.ogg" type="audio/ogg">
                <source src="./assets/audio/drop.mp3" type="audio/mpeg">
                Your browser does not support the audio element.
              </audio>
          <!-- <span v-text="'Points: ' + points" /> -->
          <section class="icon-list">
            <div v-for="point in (points-points%2)/2" :key="'heart-' + point" class="" style="display:inline-block" >
              <i class="nes-icon is-large heart"/>
            </div>
            <i v-for="point in points%2"            :key="'half_star' + point" class="nes-icon is-large is-half heart rotate"/>
            <i v-for="point in (maximumPoints-(points + points%2))/2" :key="'empty_start' + point" class="nes-icon is-large is-transparent heart rotate"></i>
          </section>

          <br />
          <div class="nes-select" style="margin:auto;width:320px;" >
            <select v-model="level" >
              <option value="0">Easy</option>
              <option value="1">Medium</option>
            </select>
          </div>
          <button
            @click="checkAnswer"
            class="nes-btn is-success "
            v-text="'Submit Answer'"
          />
          <button @click="reset" class="nes-btn is-error" v-text="'Reset'" />

        </div>
      </div>


    <dialog class="nes-dialog" id="dialog-game-over">
      <form method="dialog">
        <p class="title">Game Over</p>
      </form>
    </dialog>

    <dialog class="nes-dialog" id="dialog-win">
      <form method="dialog">
        <p class="title">You Win!</p>
        <p> Points: <span v-text="points" /> </p>
        <menu class="dialog-menu">
          <button class="nes-btn is-success">OK</button>
        </menu>
      </form>

    </dialog>

    <dialog class="nes-dialog" id="dialog-error">
      <form method="dialog">
        <p class="title">Warning</p>
        <p>Not all spots are filled!</p>
        <menu class="dialog-menu">
          <button class="nes-btn is-warning">Confirm</button>
        </menu>
      </form>
    </dialog>

    <dialog class="nes-dialog" id="dialog-wrong">
      <form method="dialog">
        <p class="title">Lost a Points</p>
        <p>One or more answers are wrong. You have lost a point</p>
        <menu class="dialog-menu">
          <button class="nes-btn is-warning">Confirm</button>
        </menu>
      </form>
    </dialog>
    <firework v-if="showFireworks" :boxHeight="'100%'" :boxWidth="'100%'"/>

  </div>



</template>

<script>
import ItemContainer from './components/ItemContainer';
import Item from './components/Item';
import Firework from './components/Firework';

export default {
  name: 'App',
  computed: {
    items: function () {
      return(this.items_list[this.level].items);
    },
    walls: function(){
      return(this.items_list[this.level].walls);
    },
    text: function(){
      return(this.items_list[this.level].text)
    },
    clues: function(){
      return(this.items_list[this.level].clues);
    }

  },
  data: function(){
    return({
      appName: 'Can You See Me?',
      sideBarName: 'Numbers',
      points: 10,
      maximumPoints: 10,
      warningDiagShow: false,
      showFireworks: false,
      level: 0,
      items_list: [
          {
            'text': 'There are five sheep in the house. We know where Sheep #1 is, but can you figure out the location of the rest of them?',
             'clues':  [' Sheep #4 can only see two sheep.', 
                      'Sheep #3 can see Sheep #1.',
                      'Sheep #5 is the sheep that can see most sheep.'],

            'walls' : [
                        'height: 400px;width: 70px;left: 300px;top: 0;',
                        'height: 300px;width: 70px;left: 398px;top: 332px;transform: rotate(-45deg);'
            ],
            'items' : [
            {
              name: '#1',
              location: 'top:200px;left:500px;',
              id: 1,
              list: 1,
              containerImg: require('@/assets/images/sheep.png')
            },
            {
              name: '#2',
              location: 'top:40px;left:150px',
              id: 2,
              list: 0,
              containerImg: require('@/assets/images/sheep.png')
            },
            {
              name: '#3',
              location: 'top:230px; left: 800px;',
              id: 3,
              list: 0,
              containerImg: require('@/assets/images/sheep.png')
            },
            {
              name: '#4',
              location: 'top:400px; left: 100px;',
              id: 4,
              list: 0,
              containerImg: require('@/assets/images/sheep.png')
            },
            {
              name: '#5',
              location: 'top:600px; left: 620px;',
              id: 5,
              list: 0,
              containerImg: require('@/assets/images/sheep.png')
            }
          ]
        }
        ,{
          'text': '',
          clues: [
            '#3 can only see two sheep.',
            '#1 and #5 can’t see each other, but they can see exactly the same sheep.',
            '#2 can see an odd number of sheep.',
            '#4 and #2 can see each other.'
          ],
          'walls': [
                        'height: 400px;width: 70px;left: 300px;top: 0;',
                        'height: 300px;width: 70px;left: 398px;top: 332px;transform: rotate(-45deg);' ,
                        'height: 200px;width: 70px;left: 700px;top: 200px;transform: rotate(50deg);'          ],

          'items':[

           {
              name: '#1',
              location: 'top:100px;left:450px;',
              id: 1,
              list: 0,
              containerImg: require('@/assets/images/sheep.png')
            },
                        {
              name: '#2',
              location: 'top:10px; left: 850px;',
              id: 2,
              list: 2,
              containerImg: require('@/assets/images/sheep.png')
            },
            {
              name: '#3',
              location: 'top:400px; left: 100px;',
              id: 3,
              list: 0,
              containerImg: require('@/assets/images/sheep.png')
            },
            {
              name: '#4',
              location: 'top:600px; left: 620px;',
              id: 4,
              list: 0,
              containerImg: require('@/assets/images/sheep.png')
            },

            {
              name: '#5',
              location: 'top:260px; left: 850px;',
              id: 5,
              list: 0,
              containerImg: require('@/assets/images/sheep.png')
            },
            {
              name: '#6',
              location: 'top:40px;left:150px',
              id: 6,
              list: 0,
              containerImg: require('@/assets/images/sheep.png')
            },

          ]
        }
      ],
      btnOps: {
        type: "triangle",
        easing: "easeOutQuart",
        size: 6,
        particlesAmountCoefficient: 4,
        oscillationCoefficient: 2,
        color: function () {
          return Math.random() < 0.5 ? "#000000" : "#ffffff";
        },
        onComplete: () => {
          console.log("complete");
        },
        onBegin: () => {
          console.log("begin");
        },
        visible: true,
        animating: false
      },
    })
  },
  components: {
    ItemContainer,
    Item,
    Firework,
  },
  methods: {
    playDropSound(){
      let sound_url = require('@/assets/audio/sheep_1.mp3')
      this.playSound(sound_url);
    },
    playSound (sound) {
      if(sound) {
        var audio = new Audio(sound);
        audio.play();
      }
    },
    reset: function(){
      for (const key in this.items) {
        this.items[key].list = 0;
      }
      this.items[0].list = 1;
    },
    checkAnswer: function(){
      let wrong = false;
      for (const key in this.items) {
        const item = this.items[key];
        if (item.list == 0){
            document.getElementById('dialog-error').showModal();
            return(false);

        }
        if (item.id != item.list){
          wrong = true
        }
      }
      this.points -= wrong;
      if(wrong && this.points !=0){
        document.getElementById('dialog-wrong').showModal(wrong);
      }

      if (this.points == 0){
        document.getElementById('dialog-game-over').showModal();
      }

      if (!wrong){
        this.showFireworks = true;
        document.getElementById('dialog-win').showModal();
      }


    },
    startDrag: (e) => {
          var evt = e['event'];
          var item = e['item']
          evt.dataTransfer.dropEffect = 'move'
          evt.dataTransfer.effectAllowed = 'move'
          evt.dataTransfer.setData('itemID', item.id)
    },

   dragEnd: function(e){
    e;
    this.className = 'animal-draggable nes-container';
  },

   dragOver: function(e){
    e.preventDefault();
  },

   dragEnter: function(e){
    e.preventDefault();
    console.log(e);
    this.classList.add('hovered');

  },

   dragLeave: function(){
    this.classList.remove('hovered');
  },

  onDrop (e) {
        console.log(e);
        var evt = e['event'];
        var list = e['list']

        //Checking if already more than 1
        var itemsInList = this.items.filter(item => item.list == list).length;
        if (itemsInList > 0){
          return(false)
        }
        const itemID = evt.dataTransfer.getData('itemID')
        const item = this.items.find(item => item.id == itemID)
        item.list = list
        this.playDropSound();
  }

  }
}
</script>

<style scoped>
.wall {
  position: absolute;
  background-color: brown;

}
</style>
