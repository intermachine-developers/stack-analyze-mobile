<template>
  <ion-page>
    <Toolbar />
    <ion-content>
      <ion-item>
        <ion-label position="floating" color="secondary">
          enter a url:
        </ion-label>
        <ion-input
          type="text"
          v-model="website"
          clearInput="true"
          autocomplete="off"
          required
        >
        </ion-input>
      </ion-item>
      <ion-grid>
        <ion-row>
          <ion-col>
            <ion-button
              @click="domainInfo"
              :disabled="validate"
              color="secondary"
              fill="outline"
              expand="block"
            >
              start-analyze
            </ion-button>
          </ion-col>
          <ion-col>
            <ion-button
              @click="reset"
              :disabled="resetStatus"
              color="danger"
              fill="outline"
              expand="block"
            >
              reset tech-stack
            </ion-button>
          </ion-col>
        </ion-row>
      </ion-grid>
      <ion-card mode="ios">
        <ion-card-header mode="md">
          <ion-card-title>Website: {{ url }}</ion-card-title>
          <ion-card-subtitle>whois info</ion-card-subtitle>
          <ion-card-content>
            <ion-item v-for="(domain, i) of info" :key="domain">
              {{ i + 1 }} - {{ domain }}
            </ion-item>
          </ion-card-content>
        </ion-card-header>
      </ion-card>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from "vue";

import {
  IonPage,
  IonContent,
  IonInput,
  IonButton,
  IonLabel,
  IonItem,
  IonCard,
  IonCardTitle,
  IonCardSubtitle,
  IonCardHeader,
  IonCardContent,
  IonGrid,
  IonRow,
  IonCol,
} from "@ionic/vue";

import axios from "axios";

import Toolbar from "@/components/Toolbar.vue";

// scripts
import presentAlert from "@/ts/alertMsg";

import { whoisRegex } from "@/ts/data";

export default defineComponent({
  name: "TechStack",
  components: {
    Toolbar,
    IonContent,
    IonPage,
    IonInput,
    IonButton,
    IonLabel,
    IonItem,
    IonCard,
    IonCardTitle,
    IonCardSubtitle,
    IonCardHeader,
    IonCardContent,
    IonGrid,
    IonRow,
    IonCol,
  },
  setup() {
    const website = ref("");
    const info = ref("");
    const url = ref("");

    const reset = () => {
      info.value = "";
      url.value = "";
    };

    const validate = computed(() =>
      website.value.match(whoisRegex) ? false : true
    );
    const resetStatus = computed(() => (info.value === "" ? true : false));

    const domainInfo = async (): Promise<void> => {
      try {
        const res = await axios.get(
          "https://stack-analyze.herokuapp.com/whois",
          {
            params: {
              url: website.value,
            },
          }
        );
        info.value = res.data;
        url.value = website.value;
      } catch (err) {
        presentAlert(err.message, "Error whois", "problem to whois");
      }
      website.value = "";
    };

    return {
      info,
      website,
      reset,
      url,
      domainInfo,
      presentAlert,
      validate,
      resetStatus,
    };
  }
});
</script>
