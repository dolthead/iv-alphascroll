<template>
    <ul class="letter-sidebar" ref="bar">
    <li v-for="letter of letters" 
        :key="letter" 
        tappable 
        @click="goToLetter(letter)"
        @dragenter="goToLetter(letter)"
    >
        <a>{{ letter }}</a>
    </li>
    </ul>
</template>

<script lang="ts">
import { createGesture } from "@ionic/core";
import { defineComponent, onMounted, ref } from "vue";
import { Haptics, ImpactStyle } from "@capacitor/haptics";

export default defineComponent({
  name: 'AlphabetScroll',
  setup(props, { emit }) {

    const letters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.split('');
    const bar = ref();
    let last = '';

    const goToLetter = (letter: string) => {
        if (letter !== last) {
            last = letter;
            emit('letterSelected', letter);
        }
    };

    onMounted(() => {
        const moveGesture = createGesture({
            el: bar.value,
            direction: 'y',
            threshold: 0,
            gestureName: 'move',
            onMove: ev => {
                const closestElem: any = document.elementFromPoint(ev.currentX, ev.currentY);
                if (closestElem && ['LI', 'A'].includes(closestElem.tagName)) {
                    const letter = closestElem.innerText;
                    if (letter) {
                        if (letter !== last) {
                            Haptics.impact({ style: ImpactStyle.Light });
                        }
                        goToLetter(letter);
                    }
                }
            },
        });
        moveGesture.enable();
    });

    return {
        letters,
        goToLetter,
        bar,
    }
  }
});
</script>

<style lang="css" scoped>
.letter-sidebar {
    position: fixed;
    top: 25%;
    z-index: 101;
    right: 0;
}

.letter-sidebar li {
    list-style: none;
    width: 40px;
    text-align: center;
    font-weight: 500;
}

.letter-sidebar li a {
    font-size: 14px;
}
</style>