<template>
  <input v-model="entrypoint" @change="fetchItems()" />
  <h1>HEADER</h1>
  <ul>
    <li @click="parent()"><a href="#">../</a></li>
    <li v-for="entry in filtered" :key="entry.name">
      <div v-if="entry.type === 'directory'">
        <a href="#" @click="move(entry.name)">{{ entry.name }}</a>
        <span>/</span>
      </div>
      <div v-if="entry.type === 'file'">
        <a :href="entrypoint + path + '/' + entry.name">
          {{ entry.name }}
        </a>
      </div>
    </li>
  </ul>
</template>

<script lang="ts">
import { Options, Vue } from "vue-class-component";

interface FsEntry {
  type: "directory" | "file";
  name: string;
  size?: number;
  mtime: string;
}

@Options({})
export default class App extends Vue {
  entrypoint = "";

  paths: string[] = [];
  get path(): string {
    return this.paths.join("/");
  }

  entries: FsEntry[] = [];

  fetchItems(): void {
    const URL = this.entrypoint + this.path;
    fetch(URL)
      .then((response: Response) => response.json())
      .then((entries: FsEntry[]) => (this.entries = entries))
      .catch((e) => console.log(e));
  }

  mounted(): void {
    this.fetchItems();
  }

  move(str: string): void {
    this.paths.push(str);
    this.fetchItems();
  }

  parent(): void {
    this.paths.pop();
    this.fetchItems();
  }
  get filtered(): FsEntry[] {
    return this.entries;
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
