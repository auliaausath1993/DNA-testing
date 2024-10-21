<template>
  <div id="app" class="container mt-5">
    <h1 class="text-center mb-4">Daftar Berita Terbaru</h1>
    <div class="mb-3">
      <input v-model="searchQuery" class="form-control" placeholder="Cari berita...">
    </div>

    
    <div class="news-grid">
      
      <div class="big-box" v-if="filteredNews[0]">
        <img :src="filteredNews[0].urlToImage || 'https://via.placeholder.com/600x400'" class="img-fluid" :alt="filteredNews[0].title">
        <div class="news-content">
          <h5>{{ filteredNews[0].title }}</h5>
          <p>{{ filteredNews[0].description }}</p>
          <p class="author">{{ filteredNews[0].author }}</p>
          <p class="published-at">{{ formatDate(filteredNews[0].publishedAt) }}</p>
          <a :href="filteredNews[0].url" class="btn btn-primary" @click="saveAndOpenNews(filteredNews[0])" target="_blank">Baca Selengkapnya</a>
        </div>
      </div>
      <div class="small-box-container">
        <div class="small-box" v-for="(news, index) in filteredNews.slice(1, 5)" :key="index">
          <img :src="news.urlToImage || 'https://via.placeholder.com/150'" class="img-fluid" :alt="news.title">
          <div class="news-content">
            <h6>{{ news.title }}</h6>
            <p class="author">{{ news.author }}</p>
            <p class="published-at">{{ formatDate(news.publishedAt) }}</p>
            <a :href="news.url" class="btn btn-secondary btn-sm" @click="saveAndOpenNews(news)" target="_blank">Baca</a>
          </div>
        </div>
      </div>

      
      <div class="small-box-container">
        <div class="small-box" v-for="(news, index) in filteredNews.slice(5, 9)" :key="index">
          <img :src="news.urlToImage || 'https://via.placeholder.com/150'" class="img-fluid" :alt="news.title">
          <div class="news-content">
            <h6>{{ news.title }}</h6>
            <p class="author">{{ news.author }}</p>
            <p class="published-at">{{ formatDate(news.publishedAt) }}</p>
            <a :href="news.url" class="btn btn-secondary btn-sm" @click="saveAndOpenNews(news)" target="_blank">Baca</a>
          </div>
        </div>
      </div>
      <div class="big-box" v-if="filteredNews[9]">
        <img :src="filteredNews[9].urlToImage || 'https://via.placeholder.com/600x400'" class="img-fluid" :alt="filteredNews[9].title">
        <div class="news-content">
          <h5>{{ filteredNews[9].title }}</h5>
          <p>{{ filteredNews[9].description }}</p>
          <p class="author">{{ filteredNews[9].author }}</p>
          <p class="published-at">{{ formatDate(filteredNews[9].publishedAt) }}</p>
          <a :href="filteredNews[9].url" class="btn btn-primary" @click="saveAndOpenNews(filteredNews[9])" target="_blank">Baca Selengkapnya</a>
        </div>
      </div>
      
      
      <div v-if="loading" class="skeleton-loader">
        <div class="big-box skeleton"></div>
        <div class="small-box-container skeleton">
          <div class="small-box" v-for="index in 4" :key="index"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      searchQuery: '',
      newsData: [],
      readNews: JSON.parse(localStorage.getItem('readNews')) || [],
      loading: true 
    };
  },
  computed: {
    filteredNews() {
      return this.newsData.filter(news => news.title.toLowerCase().includes(this.searchQuery.toLowerCase()));
    }
  },
  methods: {
    async fetchNews() {
      try {
        const apiKey = '930014a02da0492bab656a67ca0c061b'; 
        const response = await axios.get(`https://newsapi.org/v2/top-headlines?country=us&apiKey=${apiKey}`);
        this.newsData = response.data.articles;
      } catch (error) {
        console.error("Error fetching news:", error);
      } finally {
        this.loading = false; 
      }
    },
    saveReadNews(news) {
      const readNewsItem = {
        title: news.title,
        url: news.url,
        urlToImage: news.urlToImage,
        author: news.author,
        publishedAt: news.publishedAt
      };
      if (!this.readNews.some(read => read.url === news.url)) {
        this.readNews.push(readNewsItem);
        localStorage.setItem('readNews', JSON.stringify(this.readNews));
      }
    },
    saveAndOpenNews(news) {
      this.saveReadNews(news);
      window.open(news.url, '_blank');
    },
    formatDate(dateString) {
      const options = { weekday: 'short', year: 'numeric', month: 'long', day: 'numeric' };
      return new Date(dateString).toLocaleDateString('id-ID', options);
    }
  },
  mounted() {
    this.fetchNews();
  }
};
</script>

<style scoped>
/* CSS Grid Layout */
.news-grid {
  display: grid;
  grid-template-columns: 2fr 1fr;
  grid-template-rows: auto auto;
  gap: 15px;
}

/* Kotak besar */
.big-box {
  grid-column: span 1;
  background-color: #f8f9fa;
  padding: 15px;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: column;
}

.small-box-container {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-gap: 10px;
}

/* Kotak kecil */
.small-box {
  background-color: #fff;
  padding: 10px;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  text-align: center;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

/* Konten berita */
.news-content {
  flex-grow: 1;
}

/* Penyesuaian untuk judul */
.news-content h5,
.news-content h6 {
  margin: 10px 0;
}

/* Gambar */
.img-fluid {
  max-width: 100%;
  height: auto;
  border-radius: 8px;
}

/* Skeleton Loader */
.skeleton-loader {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.skeleton {
  background-color: #e0e0e0;
  border-radius: 8px;
}

.big-box.skeleton {
  height: 250px;
}

.small-box.skeleton {
  height: 100px;
}

@media (max-width: 768px) {
  .news-grid {
    grid-template-columns: 1fr;
  }

  .small-box-container {
    grid-template-columns: 1fr;
  }
}
</style>

