<template>
  <div class="container">
    <div v-if="loader.getIssue" class="text-center">
      <img src="/static/load.gif" alt="loader">
    </div>
    <div v-else-if="issue.number">
      <a href="javascript:history.go(-1)">Voltar</a>
      <h1>Issue #{{ issue.number }}</h1>
      <h2>{{ issue.title }}</h2>
      <p>{{ issue.body }}</p>
      <a :href="issue.html_url" target="_blank">Ver no Github</a>
    </div>
    <div v-else>
      <h1>404 - Página não encontrada!</h1>
    </div>
  </div>
</template>

<script>

import axios from 'axios';

export default {
  name: 'GitHubIssue',

  created() {
    this.getIssue();
  },

  data() {
    return {
      issue: {},
      loader: {
        getIssue: false,
      },
    };
  },

  methods: {
    getIssue() {
      // Start loading...
      this.loader.getIssue = true;
      const url = `https://api.github.com/repos/${this.$route.params.name}/${this.$route.params.repo}/issues/${this.$route.params.issue}`;
      axios.get(url)
        .then((response) => {
          this.issue = response.data;
        }).catch((error) => {
          // eslint-disable-next-line
          console.log(error.message);
        }).finally(() => {
          // Stop loading...
          this.loader.getIssue = false;
        });
    },
  },

};

</script>
