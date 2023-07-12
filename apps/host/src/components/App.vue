<template>
  <div class="host">
    <Navbar :apps="apps" :selected="selected" :host="host" @input="host = $event" @loadServers="loadServers" @setComponent="setComponent" />
    <component :is="dynamicComponent" />
    <div v-if="!dynamicComponent" class="host__content">
      <div class="host__title">
        <h1>Host</h1>
      </div>
    </div>
  </div>
</template>

<script lang='ts'>
import { Component, Vue } from 'vue-facing-decorator';
import Navbar from './Navbar.vue';
import { LoadRemoteModule } from '../load-remote-module';

@Component({
  components: {
    Navbar,
  },
})
export default class App extends Vue {
  public dynamicComponent: any = null;
  public host: string = process.env.APPS_URL ?? '';
  public apps: string[] = [];
  public selected: string = '';
  private loadRemoteModule = new LoadRemoteModule();

  async setComponent (selected: string) {
    this.selected = selected;
    if (!this.selected) {
      this.dynamicComponent = null;
      return;
    }
    this.dynamicComponent = (await this.loadRemoteModule.loadComponent(this.selected, './Module')).default;
  }

  async loadServers () {
    await this.loadRemoteModule.setHost(this.host).loadServers();
    this.apps = this.loadRemoteModule.apps;
  }
}
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
