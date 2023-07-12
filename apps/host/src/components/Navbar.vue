<template>
  <div class="navbar__container">
    <div class="navbar__menu">
      <input :value="host" style="width: 100%" @input="onInput" />
      <button class="btn" @click="loadServers">Download</button>
      <select :value="selected" @change="setComponent">
        <option value="">Please select one</option>
        <option v-for="(app, key) in apps" :key="key" :value="app">{{app}}</option>
      </select>
    </div>
  </div>
</template>

<script lang='ts'>
import { Component, Prop, Vue, Emit } from 'vue-facing-decorator';

type HTMLElementEvent<T extends HTMLElement> = Event & { target: T };

@Component
export default class Navbar extends Vue {
  @Prop() apps: string[];
  @Prop() host: string;
  @Prop() selected: string;

  @Emit
  loadServers (): boolean {
    return true;
  }

  @Emit
  setComponent (event: HTMLElementEvent<HTMLButtonElement>): string {
    return event.target.value;
  }

  @Emit('input')
  onInput (event: HTMLElementEvent<HTMLButtonElement>): string {
    return event.target.value;
  }
}
</script>

<style scoped lang='scss'>
.navbar {
  &__container {
    position: absolute;
    width: 100%;
  }
  &__menu {
    display: flex;
    justify-content: center;
    padding: 1rem;
    gap: 1rem;
    a {
      color: var(--white);
      font-weight: 700;
      text-decoration: none;
      transition: 1s;
      text-transform: uppercase;
      &:hover {
        color: var(--purple);
      }
    }
  }
}
</style>
