<template>
  <div class="container">
    <h1>Vue.js + Github</h1>
    <p class="lead">Página que lista issues de um repositório do Github, usando Vue.js.</p>

    <div class="row">
      <div class="col">
        <div class="form-group">
          <input v-model="username"
                 type="text" class="form-control" placeholder="github username">
        </div>
      </div>

      <div class="col">
        <div class="form-group">
          <input v-model="repository"
                 type="text" class="form-control" placeholder="github repositório">
        </div>
      </div>

      <div class="col-3">
        <div class="form-group">
          <button @click.prevent.stop="getIssues()" class="btn btn-success">GO</button>
          <button @click.prevent.stop="reset()" class="btn btn-danger">LIMPAR</button>
        </div>
      </div>
    </div>

    <br><hr><br>

    <p v-if="loader.getIssue"
       class="text-center" colspan="2">
       Carregando...
    </p>

    <template v-if="selectedIssue.id">
      <a @click.prevent.stop="clearIssue()"
         href="#">
         Voltar
      </a>
      <h2>{{ selectedIssue.title }}</h2>
      <p>{{ selectedIssue.body }}</p>
    </template>

    <table v-if="!selectedIssue.id" class="table table-sm table-bordered">
      <thead>
        <tr>
          <th width="100">Número</th>
          <th>Título</th>
        </tr>
      </thead>

      <tbody>
        <tr v-if="loader.getIssues">
          <td class="text-center" colspan="2">Carregando...</td>
        </tr>

        <tr v-if="issues.length <= 0 && !loader.getIssues">
          <td class="text-center" colspan="2">Nenhuma issue encontrada!</td>
        </tr>

        <tr v-else
            v-for="issue in issues"
            :key="issue.number">
          <td>{{ issue.number }}</td>
          <td>
            <a @click.prevent.stop="getIssue(issue.number)"
               :href="issue.html_url">
               {{ issue.title }}
            </a>
          </td>
        </tr>

      </tbody>
    </table>
  </div>
</template>

<script>

import axios from 'axios';

export default {
  name: 'GitHubIssues',

  data() {
    return {
      username: '',
      repository: '',
      issues: [],
      selectedIssue: {},
      loader: {
        getIssues: false,
        getIssue: false,
      },
    };
  },

  methods: {
    reset() {
      this.username = '';
      this.repository = '';
      this.issues = [];
      this.selectedIssue = {};
    },
    getIssues() {
      if (this.username && this.repository) {
        // Start loading...
        this.loader.getIssues = true;
        // Clear table
        this.issues = [];
        // Clear selected issue
        this.selectedIssue = {};
        const url = `https://api.github.com/repos/${this.username}/${this.repository}/issues`;
        axios.get(url)
          .then((response) => {
            this.issues = response.data;
          }).catch((error) => {
            // eslint-disable-next-line
            console.log(error.message);
          }).finally(() => {
            // Stop loading...
            this.loader.getIssues = false;
          });
      }
    },
    getIssue(issueID) {
      if (this.username && this.repository) {
        // Start loading...
        this.loader.getIssue = true;
        const url = `https://api.github.com/repos/${this.username}/${this.repository}/issues/${issueID}`;
        axios.get(url)
          .then((response) => {
            this.selectedIssue = response.data;
          }).catch((error) => {
            // eslint-disable-next-line
            console.log(error.message);
          }).finally(() => {
            // Stop loading...
            this.loader.getIssue = false;
          });
      }
    },

    clearIssue() {
      this.selectedIssue = {};
    },
  },

};

</script>
