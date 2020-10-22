<template>
  <div id="app">

	<div id="gameboard-container" class="nes-container with-title is-centered" style="background-color:green" >

		<p class="title " v-text="appName"></p>
    <!-- Walls -->
    <div class="wall-1"/>
    <div class="wall-2"/>

    <!-- Sheep -->
    <div v-for="(animal,index) in animals" :key="'animal-' + index" :style="'position:absolute;' + animal.style" >
      <img class="value-image" style="top: 90px;left:0px;" />
      <div class="value-input side-container" style="top:0px;left: 0px;" >
        <span :id='animal.name.toLowerCase()+"-container"' class="animal-container" />
      </div>
    </div>

	</div>


  <!-- Sidebar -->
	<div id="sidebar">
		<div id="animal-container" class="nes-container with-title is-centered">
			<p class="title">Animals</p>
      <!-- Dragabble Items -->
			<div v-for="animal in randomList(animals)" :id="animal.name.toLowerCase() + '-draggable'" :key="animal.name.toLowerCase() + '-draggable'" class="animal-draggable nes-container" draggable="true" >
				<div class="animal-name" >{{ animal.name }}</div>
			</div>
		</div>
  <!-- Sidebar END -->

    <!-- Options Container -->
  <div id="options-container" ondragover="dragStart(e)" class="nes-container with-title is-centered">
    <p class="title" >Info</p>
    <audio id="dropAudio">
      <source src="./assets/audio/drop.ogg" type="audio/ogg">
      <source src="./assets/audio/drop.mp3" type="audio/mpeg">
      Your browser does not support the audio element.
    </audio>
    <section class="icon-list" v-text="'Points: ' + points">

      <!--
          <i class="nes-icon is-medium star" style="margin-left:16px" v-for="point in (points-points%2)/2" :key="'full_start' + point " ></i>
          <i class="nes-icon is-medium is-half star" v-for="point in points%2" :key="'half_star' + point" ></i>
          <i class="nes-icon is-medium is-empty star" style="margin-right: 37px;" v-for="point in (totalpoints-(points + points%2))/2" :key="'empty_start' + point" ></i>
          -->
    </section>
    <br>
    <button @click="toggleFullscreen()" type="button" class="nes-btn is-primary">Toggle Fullscreen</button>
    <br>
    <button @click="checkSolutions()" type="button" class="nes-btn is-success">Check Solution</button>
  </div>
  <!-- Options Container END -->

	</div>


  <dialog class="nes-dialog" id="dialog-game-over">
    <form method="dialog">
      <p class="title">Game Over</p>
      <menu class="dialog-menu">
        <button class="nes-btn">Cancel</button>
        <button class="nes-btn is-error">Confirm</button>
      </menu>
    </form>
  </dialog>

  <dialog class="nes-dialog" id="dialog-win">
    <form method="dialog">
      <p class="title">You Win!</p>
      <menu class="dialog-menu">
        <button class="nes-btn">Cancel</button>
        <button class="nes-btn is-success">Confirm</button>
      </menu>
    </form>
  </dialog>

  <dialog class="nes-dialog" id="dialog-error">
    <form method="dialog">
      <p class="title">Warning</p>
      <p>Not all spots are filled!</p>
      <menu class="dialog-menu">
        <button class="nes-btn">Cancel</button>
        <button class="nes-btn is-warning">Confirm</button>
      </menu>
    </form>
  </dialog>


  </div>
</template>

<script>
export default {
  name: 'App',
    data: function() {
      return({
      appName: 'Can You Find Me',
      mainBackgroundImg: require('@/assets/images/background_new.png'),
      points: 10,
      totalpoints:10,
      animals: [
        {
          name: 'Second',
          image: require('@/assets/images/beaver.png'),
          style: 'top: 10px; left:150px;'
        },
        {
          name: 'Fourth',
          image: require('@/assets/images/coyote.png'),
          style: 'top: 350px; left:40px;'

        },
        {
          name: 'Fifth',
          image: require('@/assets/images/eagle.png'),
          style: 'top: 590px; left:600px;'
        },
        {
          name: 'First',
          image: require('@/assets/images/anteater.png'),
          style: 'top: 200px; left:500px;',
        },
        {
          name: 'Third',
          image: require('@/assets/images/duck.png'),
          style: 'top: 0px; left:700px;'
        },

      ],
      animals_order: {
        left: [
          'anteater',
          'beaver',
          'duck',
          'coyote',
        ],
        right: [ 
          'eagle',
          'flamingo',
          'gorilla',
          'hare'
        ]

      }
      })
    },
    methods: {
    randomList: function(rand){
      return rand.sort(function(){return 0.5 - Math.random()});
    },
      toggleFullscreen: function(event) {
          var element = document.body;

          if (event instanceof HTMLElement) {
            element = event;
          }

          var isFullscreen = document.webkitIsFullScreen || document.mozFullScreen || false;

          element.requestFullScreen = element.requestFullScreen || element.webkitRequestFullScreen || element.mozRequestFullScreen || function () { return false; };
          document.cancelFullScreen = document.cancelFullScreen || document.webkitCancelFullScreen || document.mozCancelFullScreen || function () { return false; };

          isFullscreen ? document.cancelFullScreen() : element.requestFullScreen();
        },

      checkSolutions: function(){
        var points = this.points
        var containers = document.querySelectorAll('.animal-container');
        var wrong = false;
        for (let container_key = 0; container_key < containers.length; container_key++) {
          let container = containers[container_key];

          // Return error with dialog if any of the containers are empty
          if (container.querySelector('.animal-draggable') == null){
            document.getElementById('dialog-error').showModal();
            return(false)
          }

          var container_name = container.id.
                                substring(0, container.id.length - 10);

          // Getting name (First, Second, Etc) from the item currenlty in container
          var child = container.querySelector('.animal-draggable');
          var child_name = child.id.substring(0, child.id.length - 10);

          if (container_name == child_name){
              container.className = "animal-container correct";
            } else {
              container.className = "animal-container wrong";
              wrong = true ;
            }
          if (wrong){
            points = points - 1 ; //+ 0;// - 1;
          }
          if (points == 0){
            document.getElementById('dialog-game-over').showModal();
          }

          if (!wrong){
            document.getElementById('dialog-win').showModal();
          }

          this.points = points;
        }

      },

      checkSolutions_old: function(){
        let points = this.points;
        console.log("Checking");
        console.log("Checking Solutions");
        var containers = document.querySelectorAll('.animal-container');
        console.log(containers.length);
        var container_key;
        for (container_key; container_key < containers.length; container_key++) {
          console.log(container_key);
        }

        var wrong = false;
        for (container_key = 0; container_key < containers.length; container_key++) {
          console.log('Container Key: ',container_key);
          var container = containers[container_key];
          console.log('Container:', container);
          if (container.querySelector('.animal-draggable') == null){
            document.getElementById('dialog-error').showModal();
            return(points);
          }
          var container_name = container.id.
                                substring(0, container.id.length - 10);
          var child = container.querySelector('.animal-draggable')
          var child_name = child.id.
                              substring(0, child.id.length - 10);

          console.log({
            container_name: container_name,
            child_name: child_name
          });
          console.log({
            container_name: container_name,
            child_name: child_name
          })
          if (container_name == child_name){
              container.className = "animal-container correct";
            } else {
              container.className = "animal-container wrong";
              wrong = true 
            }
          }
          if (wrong){
            points --;
          }
          if (points == 0){
            document.getElementById('dialog-game-over').showModal();
          }

          if (!wrong){
            document.getElementById('dialog-win').showModal();
          }

          this.points = points;
      }



    },
 
  components: {
    

  },
  mounted(){
    // Defining variables
  const animalDraggables = document.querySelectorAll('.animal-draggable');
  const animalContainers = document.querySelectorAll('.animal-container');
  // animalDragbables Listeners
  animalDraggables.forEach(animalDraggable => {
    animalDraggable.addEventListener('dragstart',dragStart);
    animalDraggable.addEventListener('dragend',dragEnd);
  });

  // animalContainers Listeners
    animalContainers.forEach(animalContainer => {
    animalContainer.addEventListener('dragover',dragOver);
    animalContainer.addEventListener('dragenter',dragEnter);
    animalContainer.addEventListener('dragleave',dragLeave);
    animalContainer.addEventListener('drop',dragDrop);
   }); 

    var animalsContainer = document.querySelector('#animal-container');
    animalsContainer.addEventListener('dragover',dragOver);
    animalsContainer.addEventListener('dragenter',dragEnter);
    animalsContainer.addEventListener('dragleave',dragLeave);
    animalsContainer.addEventListener('drop',dragDrop);

  function dragStart(e){
    requestAnimationFrame(() => (this.className = 'invisible'),0)
    e.dataTransfer.setData("dragging_id",e.target.id)
  }

  function dragEnd(e){
    e;
    this.className = 'animal-draggable nes-container';
  }

  function dragOver(e){
    e.preventDefault();
  }
  
  function dragEnter(e){
    e.preventDefault();
    this.classList.add('hovered');

  }

  function dragLeave(){
    this.classList.remove('hovered');
  }

  function dragDrop(e){
    e.preventDefault(e);
    this.classList.remove('hovered');
    var target_id = e.dataTransfer.getData('dragging_id');
    var animalDraggable = document.querySelector("#"+target_id);
      console.log(this.children);
    if ((animalDraggable.tagName == 'DIV')){
      //dropAudio.play();
      animalDraggable.classList.add("bounceIn");
      this.appendChild(animalDraggable);

    }
  }




  }
}
</script>

<style scoped>

</style>