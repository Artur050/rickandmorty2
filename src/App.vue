<script>
import axios from 'axios';

export default {
  data() {
    return {
      characters: [],
      currentPage: 1,
      totalPages: 1,
      nameFilter: '',
      statusFilter: ''
    };
  },
  mounted() {
    this.fetchCharacters();
  },
  computed: {
    filteredCharacters() {
      let filtered = this.characters;
      console.log('Фильтры:', this.nameFilter, this.statusFilter);
      if (this.nameFilter) {
        filtered = filtered.filter(character => character.name.toLowerCase().includes(this.nameFilter.toLowerCase()));
      }
      if (this.statusFilter) {
        filtered = filtered.filter(character => character.status === this.statusFilter);
      }
      console.log('Отфильтрованные персонажи:', filtered);
      return filtered;
    }
  },
  methods: {
    async fetchCharacters() {
      try {
        const response = await axios.get(`https://rickandmortyapi.com/api/character?page=${this.currentPage}`);
        this.characters = response.data.results;
        this.totalPages = response.data.info.pages;
      } catch (error) {
        console.error('Ошибка при загрузке персонажей:', error);
      }
    },
    getStatusColor(status) {
      if (status === 'Alive') {
        return 'alive';
      } else if (status === 'Dead') {
        return 'dead';
      } else {
        return 'unknown';
      }
    },
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
        this.fetchCharacters();
      }
    },
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
        this.fetchCharacters();
      }
    },
    applyFilters() {
      this.currentPage = 1;
      this.appliedNameFilter = this.nameFilter;
      this.appliedStatusFilter = this.statusFilter;
    }

  }
};
</script>

<template>
  <div class="filters"> 
    <input class="nameCharacter" type="text" v-model.lazy="nameFilter" placeholder="Имя персонажа">
    <select v-model="statusFilter">
      <option value="">Выберите статус</option>
      <option value="Alive">Живой</option>
      <option value="Dead">Мертвый</option>
      <option value="unknown">Неизвестно</option>
    </select>
    <button @click="applyFilters">Применить</button>
  </div>
  <div class="character-list">
    <ul class="character-grid">
      <li v-if="filteredCharacters.length === 0">Нет результатов</li>
      <li v-else v-for="character in filteredCharacters" :key="character.id" class="character-card">
        <div class="character_ImgWrapper">
          <img :src="character.image" :alt="character.name" class="character-image">
        </div>
        <div class="character-info">
          <div class="section" >
            <a :href="'https://rickandmortyapi.com/api/character/' + character.id">
              <h2>{{ character.name }}</h2>
            </a>
            <span class="status">
              <div class="status-dot" :class="getStatusColor(character.status)"></div>
              {{ character.status }} - {{ character.species }}
            </span>
          </div>
          
          <p><strong>Last known location:</strong> {{ character.location.name }}</p>
          <p><strong>First seen in:</strong> {{ character.origin.name }}</p>
        </div>
      </li>
    </ul>
  </div>
  <div class="pagination">
      <button @click="prevPage" :disabled="currentPage === 1" class="pagination-button">&#8592;</button>
      <button @click="nextPage" :disabled="currentPage === totalPages" class="pagination-button">&#8594;</button>
    </div>
</template>

<style>

.character-list {
  display: flex;
  -webkit-box-pack: center;
  justify-content: center;
  -webkit-box-align: center;
  align-items: center;
  padding: 4.5rem 0px;
  background: rgb(39, 43, 51);
  min-height: calc(-60px + 50vh);
}

.character-grid {
  display: flex;
  -webkit-box-pack: center;
  justify-content: center;
  -webkit-box-align: center;
  align-items: center;
  flex-wrap: wrap;
  max-width: 1920px;
}

.character-card {
  width: 600px;
  height: 220px;
  display: flex;
  overflow: hidden;
  background: rgb(60, 62, 68);
  border-radius: 0.5rem;
  margin: 0.75rem;
  box-shadow: rgba(0, 0, 0, 0.1) 0px 4px 6px -1px, rgba(0, 0, 0, 0.06) 0px 2px 4px
}

.character_ImgWrapper {
  flex: 2 1 0%;
  width: 100%;
}

.character-image {
  width: 100%;
  height: 100%;
  margin: 0px;
  opacity: 1;
  transition: opacity 0.5s ease 0s;
  object-position: center center;
  object-fit: cover;
}

.character-info {
  flex: 3 1 0%;
  position: relative;
  padding: 0.75rem;
  color: rgb(255, 255, 255);
  display: flex;
  flex-direction: column;
}

.section {
  flex: 1 1 0%;
  display: flex;
  flex-direction: column;
  -webkit-box-pack: center;
  justify-content: center;
}

a {
  color: rgb(245, 245, 245);
  text-decoration: none;
}

a:hover {
  color: rgb(255, 152, 0);
  text-decoration: none;
}

a.h2 {
  font-size: 1.25rem;
  margin: 0px;
  padding: 0px;
}

.status {
  display: flex;
  -webkit-box-align: center;
  align-items: center;
  text-transform: capitalize;
}


.character-info h2 {
  margin-top: 0;
}

.character-info p {
  margin-bottom: 8px;
}

.status-dot {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  display: inline-block;
  margin-right: 5px;
}


.alive {
  background-color: green;
}

.dead {
  background-color: red;
}

.unknown {
  background-color: gray;
}

.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
}

.pagination-button {
  background-color: #1f625d;
  border: none;
  color: white;
  padding: 10px 20px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  cursor: pointer;
  border-radius: 4px;
}

.pagination-button:hover {
  background-color: #45a068;
}

.pagination-button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

.filters {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 20px;
}

.filters input[type="text"],
.filters select,
.filters button {
  margin-right: 10px;
  padding: 8px 12px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 14px;
}

.filters button {
  background-color: #007bff;
  color: #fff;
  border: none;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.filters button:hover {
  background-color: #0056b3;
}

.character-list {
  display: flex;
  -webkit-box-pack: center;
  justify-content: center;
  -webkit-box-align: center;
  align-items: center;
  padding: 4.5rem 0px;
  background: rgb(39, 43, 51);
  min-height: calc(-60px + 50vh);
  overflow-x: hidden;
}

.character-grid {
  display: flex;
  -webkit-box-pack: center;
  justify-content: center;
  -webkit-box-align: center;
  align-items: center;
  flex-wrap: wrap;
  max-width: 100%; 
  padding: 0 15px; 
  box-sizing: border-box; 
}

.character-card {
  width: 600px;
  height: 220px;
  display: flex;
  overflow: hidden;
  background: rgb(60, 62, 68);
  border-radius: 0.5rem;
  margin: 0.75rem;
  box-shadow: rgba(0, 0, 0, 0.1) 0px 4px 6px -1px, rgba(0, 0, 0, 0.06) 0px 2px 4px;
}

.character_ImgWrapper {
  flex: 2 1 0%;
  width: 100%;
}

.character-image {
  width: 100%;
  height: 100%;
  margin: 0px;
  opacity: 1;
  transition: opacity 0.5s ease 0s;
  object-position: center center;
  object-fit: cover;
}

.character-info {
  flex: 3 1 0%;
  position: relative;
  padding: 0.75rem;
  color: rgb(255, 255, 255);
  display: flex;
  flex-direction: column;
}

.section {
  flex: 1 1 0%;
  display: flex;
  flex-direction: column;
  -webkit-box-pack: center;
  justify-content: center;
}

a {
  color: rgb(245, 245, 245);
  text-decoration: none;
}

a:hover {
  color: rgb(255, 152, 0);
  text-decoration: none;
}

a.h2 {
  font-size: 1.25rem;
  margin: 0px;
  padding: 0px;
}

.status {
  display: flex;
  -webkit-box-align: center;
  align-items: center;
  text-transform: capitalize;
}

.character-info h2 {
  margin-top: 0;
}

.character-info p {
  margin-bottom: 8px;
}

.status-dot {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  display: inline-block;
  margin-right: 5px;
}

.alive {
  background-color: green;
}

.dead {
  background-color: red;
}

.unknown {
  background-color: gray;
}

.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
}

.pagination-button {
  background-color: #1f625d;
  border: none;
  color: white;
  padding: 10px 20px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  cursor: pointer;
  border-radius: 4px;
}

.pagination-button:hover {
  background-color: #45a068;
}

.pagination-button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

.filters {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 20px;
  flex-wrap: wrap;
}

.filters input[type="text"]
{
  max-width: 93%;
}
.filters select,
.filters button {
  margin-right: 10px;
  padding: 8px 12px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 14px;
  flex: 1 1 auto; 
  max-width: 100%; 
}

.filters button {
  background-color: #007bff;
  color: #fff;
  border: none;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.filters button:hover {
  background-color: #0056b3;
}

/* Медиа-запросы для мобильных устройств */
@media (max-width: 768px) {
  .character-card {
    width: 100%;
    height: auto;
    flex-direction: column;
    margin: 0.5rem 0;
  }

  .character_ImgWrapper {
    flex: none;
    width: 100%;
  }

  .character-info {
    flex: none;
    padding: 1rem;
  }

  .filters {
    flex-direction: column;
    align-items: flex-start;
    padding: 0 15px; 
  }

  .filters input[type="text"],
  .filters select,
  .filters button {
    margin: 5px 0;
    width: 100%;
  }

  .pagination-button {
    width: 100%;
    margin: 5px 0;
  }
}
</style>