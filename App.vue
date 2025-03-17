<template>
  <div class="container ">
    <div class="search-container">
      <div class = "name">NCU Search</div>
      <div class="search-bar">
        <div class="input-group">
          
          <input v-model="searchQuery" type="text" class="form-control form-control-sm search-input" placeholder="請輸入搜尋內容..."/>
          <img src="https://cdn-icons-png.freepik.com/512/10629/10629681.png" class="search-icon" />
        </div>
      </div>
      <div class="user">
        <img  src="https://i.pinimg.com/236x/db/2e/33/db2e331c47a0e566e261ddce26d93721.jpg"alt="profile photo"class="rounded-circle" width="110" />
      </div>
    </div>

 
   

    <p class="text-muted mt-3">搜尋結果總數: {{ filteredProjects.length }}</p>

    <!-- 排序方式 -->
    <div class="dropdown mb-3">
      <button class="btn btn-outline-primary dropdown-toggle" type="button" id="sortDropdown" data-bs-toggle="dropdown">
        排列方式
      </button>
      <ul class="dropdown-menu">
        <li><a class="dropdown-item" @click="sortBy('created_at')">依時間排序</a></li>
        <li><a class="dropdown-item" @click="sortBy('title')">依標題排序</a></li>
      </ul>
    </div>

    <!-- 搜尋結果列表 -->
    <div v-for="(project, index) in paginatedProjects" :key="index" class="card mb-3">
      <div class="card-body">
        <a :href="project.url" class="card-title text-primary h5">{{ project.title }}</a>
        <p class="card-text text-muted">{{ project.text }}</p>
      </div>
    

    <!-- 載入更多按鈕 -->
    
  </div class="more">
  <button v-if="hasMore" @click="loadMore" class=" btn-primary ">載入更多</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      searchQuery: "",
      projects: [],
      sortKey: "created_at",
      itemsPerPage: 10,
      currentPage: 1,
    };
  },
  computed: {
    filteredProjects() {
    return this.projects.map(p => ({
      ...p,
      title: p.title.trim() ? p.title : "No Title" // 如果 title 為空，則設為 "No Title"
    })).filter(p => p.title.includes(this.searchQuery) || p.text.includes(this.searchQuery));
  },
    sortedProjects() {
      return [...this.filteredProjects].sort((a, b) => {
        if (this.sortKey === 'created_at'){
          return new Date(b[this.sortKey]) - new Date(a[this.sortKey]);
        } else {
          return a[this.sortKey].localeCompare(b[this.sortKey]);
        }
      });
    },
    paginatedProjects() {
      return this.sortedProjects.slice(0, this.currentPage * this.itemsPerPage);
    },
    hasMore() {
      return this.paginatedProjects.length < this.sortedProjects.length;
    },
  },
  methods: {
    async fetchProjects() {
      try {
        const response = await fetch("/data.json");
        if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
        this.projects = await response.json();
      } catch (error) {
        console.error("載入專案 JSON 失敗:", error);
        this.projects = [];
      }
    },
    sortBy(key) {
      this.sortKey = key;
    },
    loadMore() {
      this.currentPage++;
    },
  },
  mounted() {
    this.fetchProjects();
  },
};
</script>

<style>
/* 限制最大寬度，讓畫面更集中 */
.container {
  max-width: 90%;
}

/* 美化 "NCU Search" */
.name {
  font-size: 28px;
  font-weight: bold;
  text-transform: uppercase;
  letter-spacing: 1px;
  color: #0d6efd;
  background: -webkit-linear-gradient(45deg, #0d6efd, #6610f2);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  margin-right: auto; /* 讓 NCU Search 靠左 */
}

/* 調整搜尋列的位置 */
.search-container {
  display: flex;
  align-items: center;
  justify-content: space-between; /* 讓左右兩側對齊 */
  margin: 20px auto;
  max-width: 100%;
}

/* 搜尋欄美化 */
.search-bar {
  flex: 1;
  display: flex;
  align-items: center;
  padding: 8px;
  border-radius: 24px;
  box-shadow: 0 1px 6px rgba(0, 0, 0, 0.2);
  background-color: white;
  margin-left: 50px;
  margin-right: 80px;

}

/* 搜尋輸入框 */
.search-input {
  flex: 1;
  border: none;
  outline: none;
  padding: 8px 12px;
  font-size: 16px;
  
}

/* 搜尋圖示 */
.search-bar img {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  margin-left: 20px;
}

/* 調整頭像，使其固定在右側 */
.user {
  margin-left: auto; /* 讓頭像靠右 */
  display: flex;
  align-items: center;
}

.user img {
  width: 50px;
  height: 50px;
  border: 3px solid #0d6efd;
  padding: 3px;
}

.card {
  border-radius: 10px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.more{
  display: flex;
  align-items: center;
}
.btn-primary {
  display: block;
  width: 30%;
  margin: 20px auto; /* 讓按鈕置中 */
  padding: 10px;
  font-size: 18px;
  font-weight: bold;
  border-radius: 25px;
  border-color:  #000000;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.2);
  
}
.btn-primary:hover{
background-color: #000000;
border-color:  white;
color: aliceblue;
}
</style>