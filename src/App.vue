<template>
  <div id="app">
    <Notebook
      @change-page="changePage"
      @new-page="newPage"
      :pages="pages"
      :activePage="index"
    />
    <Page
      @save-page="savePage"
      @delete-page="deletePage"
      :page="pages[index]"
    />
  </div>
</template>

<script>
import Notebook from "./components/Notebook.vue";
import Page from "./components/Page.vue";
// import Firebase from "firebase";

// Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
import { getAnalytics } from "firebase/analytics";
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries


const firebaseConfig = {
  apiKey: "AIzaSyA4YTHV06MZS8tZEQd_YAbzl4nW1TdFx0Y",
  authDomain: "notebook-4292a.firebaseapp.com",
  projectId: "notebook-4292a",
  storageBucket: "notebook-4292a.appspot.com",
  messagingSenderId: "673271881222",
  appId: "1:673271881222:web:a53c53cc184ff2eb6ced87",
  measurementId: "G-0WHCTPWYPR",
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);
const analytics = getAnalytics(app);

export default {
  name: "app",
  components: {
    Notebook,
    Page,
  },
  data: () => ({
    pages: [],
    index: 0,
  }),
  mounted() {
    database.once("value", (pages) => {
      pages.forEach((page) => {
        this.pages.push({
          ref: page.ref,
          title: page.child("title").val(),
          content: page.child("content").val(),
        });
      });
    });
  },

  methods: {
    newPage() {
      this.pages.push({
        title: "",
        content: "",
      });
      this.index = this.pages.length - 1;
    },
    changePage(index) {
      this.index = index;
    },
    savePage() {
      // nothing as of yet
      const page = this.pages[this.index];
      if (page.ref) {
        this.updateExistingPage(page);
      } else {
        this.insertNewPage(page);
      }
    },
    deletePage() {
      const ref = this.pages[this.index].ref;
      ref && ref.remove();
      this.pages.splice(this.index, 1);
      this.index = Math.max(this.index - 1, 0);
    },
    updateExistingPage(page) {
      page.ref.update({
        title: page.title,
        content: page.content,
      });
    },
    insertNewPage(page) {
      page.ref = database.push(page);
    },
  },
};
</script>

<style>
html,
body,
#app {
  height: 100%;
}

body {
  margin: 0;
}

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  display: flex;
  flex-direction: row;
}
</style>
