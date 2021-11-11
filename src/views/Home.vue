<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>{{ pageTitle }}</ion-title>
      </ion-toolbar>
    </ion-header>
    
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">{{ pageTitle }}</ion-title>
        </ion-toolbar>
        <ion-toolbar>
          <div class="search">
            <ion-input placeholder="Search">
              <ion-icon :icon="search"></ion-icon>
            </ion-input>
          </div>
        </ion-toolbar>
      </ion-header>

      <ion-item-group v-for="group of groupedData" :key="group.key">
        <ion-item-divider sticky>
          <ion-label :data-group="group.key">
            {{ group.key }}
          </ion-label>
        </ion-item-divider>
        <ion-item v-for="user of group.users" :key="user.uid" lines="none">
          <ion-label>
            {{ user.firstName }} <b>{{ user.lastName }}</b>
          </ion-label>
        </ion-item>
      </ion-item-group>

      <alphabet-scroll @letter-selected="goToLetter"></alphabet-scroll>
    </ion-content>

  </ion-page>
</template>

<script lang="ts">
import { IonContent, IonHeader, IonPage, IonTitle, IonToolbar, IonIcon, IonInput, IonItemGroup, IonItem, IonItemDivider, IonLabel } from '@ionic/vue';
import { defineComponent, ref, onBeforeUpdate } from 'vue';
import data from '../data.json';
import { search } from 'ionicons/icons';
import AlphabetScroll from '../components/alphabet-scroll.vue';

export default defineComponent({
  name: 'Home',
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar,
    IonIcon,
    IonInput,
    IonItemGroup,
    IonItem,
    IonItemDivider,
    IonLabel,
    AlphabetScroll
  },
  setup() {
    const pageTitle = 'Ionic Vue Alpha Scroll';
    const groupedData: any[] = [];

    const sorted = data.sort((a,b) => {
      if (a.last_name + a.first_name < b.last_name + b.first_name) { return -1; }
      if (a.last_name + a.first_name > b.last_name + b.first_name) { return 1; }
      return 0;
    });

    let last = '';
    sorted.forEach(({ first_name: firstName, last_name: lastName, uid }) => {
      if (lastName[0] !== last) {
        last = lastName[0];
        groupedData.push({ key: last, users: [] });
      }
      groupedData[groupedData.length - 1].users.push({ firstName, lastName, uid });
    });

    const goToLetter = (letter: string) => {
      // document.querySelector(`[data-group="${ letter }"]`)?.scrollIntoView();
      const dataGroup = document.querySelector(`[data-group="${ letter }"]`);
      if (dataGroup) {
        dataGroup.scrollIntoView();
      }
    };

    return {
      pageTitle,
      search,
      groupedData,
      goToLetter,
    }
  }

});
</script>

<style lang="scss" scoped>
.search {
  display: flex;

  ion-input {
    margin: 10px;
    --padding-start: 10px;
    --background: #e7e6e8;
    border-radius: 8px;;
  }

  ion-icon {
    margin-left: 10px;
  }
}
</style>