<template>
  <div id="app">
    <Header />
    <SearchForm v-on:search="search" />
    <SearchResults
      v-if="videos.length > 0"
      v-bind:videos="videos"
      v-bind:reformattedSearchString="reformattedSearchString"
    />
    <Pagination
      v-if="videos.length > 0"
      v-bind:prevPageToken="api.prevPageToken"
      v-bind:nextPageToken="api.nextPageToken"
      v-on:prev-page="prevPage"
      v-on:next-page="nextPage"
    />
  </div>
</template>

<script>
import axios from "axios";
import Header from "./components/layout/Header";
import SearchForm from "./components/SearchForm";
import SearchResults from "./components/SearchResults";
import Pagination from "./components/Pagination";
import moment from "moment";

export default {
  name: "App",
  components: {
    Header,
    SearchForm,
    SearchResults,
    Pagination
  },
  data() {
    const today = new Date();
    const pdate = moment(today).subtract(6, "month");
    return {
      videos: [],
      reformattedSearchString: "",
      api: {
        baseUrl: "https://www.googleapis.com/youtube/v3/search?",
        part: "snippet",
        type: "video",
        order: "viewCount",
        maxResults: 25,
        q: "",
        publishedAfter: pdate.toISOString(),
        key: "AIzaSyAsErzsesQAWxdkT7OAmS4QNqHDNIEuenc",
        prevPageToken: "",
        nextPageToken: ""
      }
    };
  },
  methods: {
    search(searchParams) {
      this.reformattedSearchString = searchParams.join(" ");
      this.api.q = searchParams.join("+");
      const {
        baseUrl,
        part,
        type,
        order,
        maxResults,
        q,
        key,
        publishedAfter
      } = this.api;
      const apiUrl = `${baseUrl}part=${part}&type=${type}&publishedAfter=${publishedAfter}&order=${order}&q=${q}&maxResults=${maxResults}&key=${key}`;
      this.getData(apiUrl);
    },
    prevPage() {
      const {
        baseUrl,
        part,
        type,
        order,
        maxResults,
        q,
        key,
        prevPageToken
      } = this.api;
      const apiUrl = `${baseUrl}part=${part}&type=${type}&order=${order}&q=${q}&maxResults=${maxResults}&key=${key}&pageToken=${prevPageToken}`;
      this.getData(apiUrl);
    },
    nextPage() {
      const {
        baseUrl,
        part,
        type,
        order,
        maxResults,
        q,
        key,
        nextPageToken
      } = this.api;
      const apiUrl = `${baseUrl}part=${part}&type=${type}&order=${order}&q=${q}&maxResults=${maxResults}&key=${key}&pageToken=${nextPageToken}`;
      this.getData(apiUrl);
    },
    getData(apiUrl) {
      axios
        .get(apiUrl)
        .then(res => {
          this.videos = res.data.items;
          this.api.prevPageToken = res.data.prevPageToken;
          this.api.nextPageToken = res.data.nextPageToken;
        })
        .catch(error => console.log(error));
    }
  }
};
</script>

<style></style>
