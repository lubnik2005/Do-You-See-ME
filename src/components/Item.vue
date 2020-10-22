<template>
    <div class="item-draggable nes-container"
        draggable
        @dragstart='startDrag($event, item)'
    >
        <div v-text="item.name" class="item-name" />
    </div>
</template>

<style scoped lang="scss">
    .nes-container:hover {
            box-shadow: 0px 0px 10px grey;

    }
</style>

<script>
export default {
    name: 'Item',
    props: ['item'],
    data: function(){
        return({

        })
    },
    methods: {
        startDrag: function(event,item){
            this.$emit('emittingDrag', {event:event,item:item})
        }
    }
}
</script>