<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>{{ pageTitle }}</ion-title>
      </ion-toolbar>
    </ion-header>
    
    <ion-content :scrollY="!scrolling">
      <ion-toolbar class="search">
        <ion-searchbar></ion-searchbar>
      </ion-toolbar>

      <ion-item-group v-for="group of groupedData" :key="group.key">
        <ion-item-divider>
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

      <alphabet-scroll @letter-selected="goToLetter" @scrolling-letter="setScrolling" @touchmove.prevent></alphabet-scroll>
    </ion-content>

  </ion-page>
</template>

<script lang="ts" setup>

  import { IonContent, IonHeader, IonPage, IonTitle, IonToolbar, IonSearchbar, IonItemGroup, IonItem, IonItemDivider, IonLabel } from '@ionic/vue';
  import data from '../data.json';
  import AlphabetScroll from '../components/alphabet-scroll.vue';

  const pageTitle = 'Ionic Vue setup script';
  const groupedData: any[] = [];
  let scrolling = false;

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
    const dataGroup = document.querySelector(`[data-group="${ letter }"]`);
    if (dataGroup) {
      dataGroup.scrollIntoView({ behavior: 'smooth' });
    }
  };

  const setScrolling = (ev: boolean) => { scrolling = ev; };

</script>

<style lang="scss" scoped>
.search {
  padding-top: 12px;
}
</style>