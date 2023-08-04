<template>
  <div class="host">
    <navbar-el>
      <input :value="host" style="width: 100%" @input="setHost" />
      <button-el class="btn" @click="loadServers">Download</button-el>
      <select :value="selected" @change="setComponent">
        <option value="">Please select one</option>
        <option v-for="(app, key) in apps" :key="key" :value="app">{{app}}</option>
      </select>
    </navbar-el>
    <component :is="dynamicComponent" />
    <div v-if="!dynamicComponent" class="host__content">
      <div class="host__title">
        <h1>Host</h1>
      </div>
    </div>
  </div>
</template>

<script lang='ts'>
import { Ref, ref, shallowRef } from 'vue';
import { LoadRemoteModule } from '@libs/utils';

type HTMLElementEvent<T extends HTMLElement> = Event & { target: T };

export default {
  setup () {
    const loadRemoteModule = new LoadRemoteModule();
    const dynamicComponent: Ref<any> = shallowRef(null);
    const host: Ref<string> = ref(process.env.APPS_URL ?? '');
    const apps: Ref<string[]> = ref([]);
    const selected: Ref<string> = ref('');

    function setHost (event: HTMLElementEvent<HTMLButtonElement>): void {
      host.value = event.target.value;
    }

    async function setComponent (event: HTMLElementEvent<HTMLButtonElement>): Promise<void> {
      selected.value = event.target.value;
      if (!selected.value) {
        dynamicComponent.value = null;
        return;
      }
      dynamicComponent.value = (await loadRemoteModule.loadComponent(selected.value, './Module')).default;
    }

    async function loadServers (): Promise<void> {
      await loadRemoteModule.setHost(host.value).loadServers();
      apps.value = loadRemoteModule.apps;
    }

    return { host, setHost, loadServers, apps, selected, dynamicComponent, setComponent };
  },
};
</script>

<style scoped lang='scss'>
.host {
  background: var(--gr-azure-pink);
  width: 100%;
  height: 100%;

  &__content {
    display: grid;
    justify-content: center;
    align-content: center;
    width: 100%;
    height: 100%;

    h1 {
      font-size: var(--font-size-h1);
      width: max-content;
      text-transform: uppercase;
      background: var(--teal);
      background: var(--gr-teal-blue);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }
  }
}
</style>
