
  <template>
    <ion-page>
      <ion-header>
        <ion-toolbar>
          <ion-title>Tugas 3 | Cryptocurrency</ion-title>
        </ion-toolbar>
      </ion-header>
      
      <ion-content>  
        <div class="table-container">
          <!-- button untuk trigger axios ambil data -->
          <div class="content-center">
            <ion-button @click="fetchData">Refresh</ion-button>
          </div>

          <!-- set tampilan saat loading data -->
          <div v-if="loading" class="loading">Loading data...</div>

          <!-- tampilkan datatable jika data sudah ada -->
          <div v-else-if="paginatedData.length > 0">
            <table class="datatable">
              <tbody>
                <!-- loop data -->
                <tr v-for="coin in paginatedData" :key="coin.id">
                  <td class="rank">
                    <!-- tampilkan judul statis -->
                    <span>Rank</span>
                    <!-- ambil data rank secara dinamis -->
                    <h3>{{ coin.rank }}</h3>
                  </td>
                  <td>
                    <!-- ambil data name & symbol secara dinamis -->
                    <span>{{ coin.name }}</span>
                    <h3>{{ coin.symbol }}</h3>
                  </td>
                  <td>
                    <!-- tampilkan judul secara statis -->
                    <span>USD</span>
                    <!-- ambil data price secara dinamis -->
                    <h3>{{ parseFloat(coin.price_usd).toFixed(2) }}</h3>
                  </td>
                </tr>
              </tbody>
            </table>

            <!-- button untuk control pagination -->
            <div class="pagination-controls">
              <button class="pagination-btn" :disabled="currentPage === 1" @click="currentPage--">Previous</button>
              <span>Page {{ currentPage }} of {{ totalPages }}</span>
              <button class="pagination-btn" :disabled="currentPage === totalPages" @click="currentPage++">Next</button>
            </div>
          </div>
        </div>
      </ion-content>
    </ion-page>
  </template>

  <script>
  import axios from "axios";
  import { defineComponent } from 'vue';

  export default defineComponent ({
    name: 'HomePage',
    data() {
      return {
        datas: [], // data yng akan disimpan dari api
        loading: false, // state loading
        currentPage: 1, // halaman yng tampil dari pagination
        perPage: 10, // jumlah item per pagination
      };
    },
    computed: {
      // kalkulasi jumlah halaman
      totalPages() {
        return Math.ceil(this.datas.length / this.perPage);
      },
      // olah data sesuai pagination
      paginatedData() {
        const start = (this.currentPage - 1) * this.perPage;
        return this.datas.slice(start, start + this.perPage);
      },
    },
    mounted() {
      this.fetchData()
    },
    methods: {
      // fetch data dari api menggunakan axios
      async fetchData() {
        try {
          this.loading = true;
          const response = await axios.get("https://api.coinlore.net/api/tickers/");
          // store resujt dari api
          this.datas = response.data.data;
          // trigger reset pagination
          this.currentPage = 1;
        
        // catch jika ada error dari api
        } catch (error) {
          console.error("Error fetching data:", error);
        // jika selesai, set state loading menjadi false
        } finally {
          this.loading = false;
        }
      },
    },
  });
  </script>

  <style scoped>
  /* row container untuk tabel */
  .table-container {
    padding: 1rem;
  }

  /* style tabel */
  .datatable {
    width: 100%;
    border-collapse: collapse;
    margin-top: 1rem;
  }

  .datatable th,
  .datatable td {
    padding: 0.8rem;
    text-align: left;
  }

  .datatable th {
    background-color: #f4f4f4;
    font-weight: bold;
  }

  .datatable tr {
    background-color: #faebbb;
    border: 1px solid #ffbe0d;
  }
  
  .datatable td h3 {
    font-weight: bold;
    margin-top: 10px;
    margin-bottom: 10px;
  }
  
  .rank {
    text-align: center !important;
  }

  .datatable tr:hover {
    background-color: #f1f1f1;
  }

  .loading {
    text-align: center;
    font-size: 1.2rem;
    font-weight: bold;
    margin-top: 1rem;
  }

  .pagination-controls {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-top: 1rem;
  }

  .pagination-btn {
    padding: 0.5rem 1rem;
    font-size: 1rem;
    background-color: #3880ff;
    color: #fff;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }

  .pagination-btn:disabled {
    background-color: #cccccc;
    cursor: not-allowed;
  }

  .pagination-btn:hover:not(:disabled) {
    background-color: #3171e0;
  }
  
  .table-container .content-center {
    display: grid;
    justify-content: center;
  }
  
  .content-center button {
    padding: 10px 40px !important;
    border-radius: 7px !important;
  }

  </style>
