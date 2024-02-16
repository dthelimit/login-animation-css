<template>
    <Sidebar />
    <div class="flex flex-row">
      <div class="bg-yellow-100 w-[290px]">
  
      </div>
      <div class="left-[100px] h-screen w-full ml-[0px] p-10 flex flex-col items-center">
        <div class="p-10 w-full">
          <div class="flex flexx-row justify-between mb-[10px]">
            <h1 class="text-left text-[37px] font-bold text-[#42656b]">Daftar Karyawan Aktif</h1>
            <div class="flex flex-row justify-end py-3">
              <div class="flex items-center bg-white rounded-[20px] px-[30px]  overflow-hidden border border-[#42656B]">
                <span class="mr-2">
                  <img src="../assets/image/Icon Search.svg" alt="Search Icon" class="w-6 h-6" />
                  <!-- Ganti dengan path dan ukuran gambar sesuai kebutuhan -->
                </span>
                <input v-model="search" class="w-full p-1 focus:outline-none" type="text" placeholder="Search" />
              </div>
              <button @click="openAddModal"
                  class="bg-[#42656B] hover:bg-[#355156] text-white font-cairo py-[5px] px-4 rounded-full ml-4">
                      <span class="flex items-center">
                        <i class="fas fa-plus mr-2"></i>
                        Tambah Staff
                      </span>
                </button>
            </div>
          </div>
  
          <!-- Tabel  -->
          <div class="flex justify-center w-full">
            <table class="w-full bg-white shadow-md overflow-hidden rounded-t-[20px] my-">
              <thead class="text-white bg-[#42656b] h-[50px]" style="background-color: #42656B;">
                <tr class="border-b">
                  <th class="text-center px-2 py-2">Id Karyawan</th>
                  <th class="text-center px-2 py-2">Nama Karyawan</th>
                  <th class="text-center px-2 py-2">Alamat</th>
                  <th class="text-center px-2 py-2">Jabatan</th>
                  <th class="text-center px-2 py-2">Aksi</th>
                  <th class="text-center px-15 py-2">Foto Staff</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(item, index) in filteredItems" :key="index" class="border-b hover:bg-gray-100">
                  <td class="px-4 py-5 text-center">{{ isEditing && item.id === editedItem.id ? '' : item.id_karyawan }}</td>
                  <td class="px-4 py-5 text-center">{{ isEditing && item.id === editedItem.id ? '' : item.nama_karyawan }}</td>
                  <td class="px-4 py-5 text-center">{{ isEditing && item.id === editedItem.id ? '' : item.alamat }}</td>
                  <td class="px-4 py-5 text-center">{{ isEditing && item.id === editedItem.id ? '' : item.jabatan }}</td>
                  <td class="px-4 py-5 text-center">
                    <span class="flex items-center justify-center">
                      <!-- Edit button -->
                      <img @click="editItem(item, index)" src="../assets/image/edit.svg"
                        class="cursor-pointer text-blue-500 mr-2" v-if="!isEditing" alt="Edit Icon" title="Edit">
                      <!-- Delete Button -->
                      <img @click="showDeleteConfirmation(item.id_karyawan)" src="../assets/image/delete.svg"
                        class="cursor-pointer text-red-500" v-if="!isEditing" alt="Delete Icon" title="Hapus Data">
                    </span>
                  </td>
                  <td class="px-4 py-5 text-center w-1/5">
                    <div class="flex items-center justify-center gap-2" v-if="item.image">
                      <img :src="item.image" alt="image" class="w-40 h-40 rounded-full mr-2 object-cover mb-3">
                    </div>
                    {{ isEditing && item.id === editedItem.id ? '' : '' }}
                    <div class="flex items-center justify-center">
                      <input 
                        style="display: none" 
                        type="file" 
                        @change="onFileSelected"
                        ref="fileInput">
                      <button @click="pickFile(index)" class="ml-auto bg-gray-300 hover:bg-gray-400 text-gray-800 py-2 px-4 border border-gray-400 rounded" title="Select File">Select File</button>
                      <!-- <button @click="onUpload">Upload</button> -->
                      <img @click="onUpload(index)" src="../assets/image/upload.svg"
                        class="cursor-pointer text-blue-500 ml-auto" alt="Upload" title="Upload File">
                    </div>
                    <div class="flex items-center justify-center" v-if="item.uploadSuccessMessage">
                      <p>{{ item.uploadSuccessMessage }}</p>
                    </div>
                    <!-- <div class="flex items-center justify-center" v-if="uploadedFileName">
                      <img :src="getUploadedFileUrl()" alt="Uploaded File" />
                    </div> -->
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
  
          <!--  Row Pages  -->
          <div class="flex flex-row items-center justify-center mt-4 text-[#182951]">
            <div class="tes ml-auto flex flex-row">
              <span class="m-2 text-[23px]">Rows per Page:</span>
              <div class="flex items-center">
                <div class="border border-[#182951] justify-normal flex items-center rounded-[15px]">
                  <div>
                    <span class="flex flex-row items-center">
                      <div class="w-[40px]">
                        <input v-model="currentPage" @input="changePage" type="text"
                          class="w-full text-center outline-none text-2xl bg-transparent" pattern="\d*" />
                      </div>
                      <span class="flex flex-col p-1">
                        <span class="custom-arrow" @click="decrementPage">
                          <img src="../assets/image/uparrow.svg" alt="" style="width: 13px; height: 13px;">
                        </span>
                        <span class="custom-arrow" @click="incrementPage">
                          <img src="../assets/image/downarrow.svg" alt="" style="width: 13px; height: 13px;">
                        </span>
                      </span>
                    </span>
                  </div>
                </div>
                <!-- <span class="ml-2">of {{ totalPages() }}</span> -->
              </div>
            </div>
          </div>
  
          <!-- form edit -->
          <div v-if="isEditing" class="fixed inset-0 flex items-center justify-center z-50">
            <div class="modal-overlay absolute w-full h-full bg-gray-900 opacity-50"></div>
            <div class="modal-container bg-white w-11/12 md:max-w-md mx-auto rounded shadow-lg z-50 overflow-y-auto">
              <div class="modal-content py-4 text-left px-6">
                <h1 class="text-2xl font-bold mb-4 text-center">Edit Data</h1>
                <div class="mb-4">
                  <label class="block text-sm font-cairo mb-2">Id Karyawan</label>
                  <input v-model="editedItem.id_karyawan" class="w-full p-2 border rounded" type="number" />
                </div>
                <div class="mb-4">
                  <label class="block text-sm font-cairo mb-2">Nama Staff</label>
                  <input v-model="editedItem.nama_karyawan" class="w-full p-2 border rounded" type="text" />
                </div>
                <div class="mb-4">
                  <label class="block text-sm font-cairo mb-2">Alamat</label>
                  <input v-model="editedItem.alamat" class="w-full p-2 border rounded" type="text" />
                </div>
                <div class="mb-4">
                  <label class="block text-sm font-cairo mb-2">Jabatan</label>
                  <input v-model="editedItem.jabatan" class="w-full p-2 border rounded" type="text" />
                </div>
                <button @click="saveEdit" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
                  Simpan
                </button>
                <button @click="cancelEdit" class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded ml-2">
                  Batal
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  
  <!-- ... (bagian script tetap sama) ... -->
  
  
  <script>
  import axios from 'axios';
  import Sidebar from "../components/sidebar.vue";
  
  export default {
    components: { Sidebar },
  
    data() {
      return {
        selectedFile: null,
        uploadedFileName: null,
        uploadSuccessMessage: "",
        search: '',
        items: [],
        isEditing: false,
        editedItem: {
          id: null,
          nama_karyawan: '',
          alamat: '',
          id_karyawan: '',
          jabatan: '',
          image: null,
        },
      };
    },
    methods: {
  
      // getUploadedFileUrl() {
      //     // Ganti URL sesuai dengan endpoint backend Anda
      //     const baseUrl = 'http://localhost:3000/staff/datastaff/';
      //     return baseUrl + this.uploadedFileName;
      //   },
  
      pickFile() {
        
        // Mencari elemen input file dengan menggunakan querySelector
        const fileInput = document.querySelector('input[type="file"]');
  
        // Mengecek apakah fileInput ditemukan sebelum memanggil click
        if (fileInput) {
          // Memanggil metode click pada elemen input file yang disembunyikan
          fileInput.click();
        } else {
          console.error('Error: File input element not found.');
        }
      },
      
      onFileSelected(event) {
        this.selectedFile = event.target.files[0]
      },
      //Melakukan Pengiriman File Gambar ke Server
      onUpload(index) {
        const fd = new FormData();
        fd.append('file', this.selectedFile, this.selectedFile.name)
        axios.post('http://localhost:3000/staff/upload' , fd, {
          onUploadProgress: uploadEvent => {
            console.log('Upload Progres: ' + Math.round(uploadEvent.loaded / uploadEvent.total * 100) + '%')
          }
        })
          .then(res => {
            this.uploadedFileName = res.data.filename;
  
            const imageUrl = `http://localhost:3000/staff/foto/${res.data.filename}`;
            this.items[index].image = imageUrl;
  
            // Perbarui data di server
            this.updateServerData(index, { image: imageUrl });
  
            // Tambahkan pesan keberhasilan ke objek karyawan di dalam array items
            this.items[index].uploadSuccessMessage = "Upload File Staff Berhasil"
            setTimeout(() => {
              this.items[index].uploadSuccessMessage = false
            }, 4000); //4000 milidetik : 4 detik
          })
          .catch(error => {
            console.error(error)
          })
      },
  
      // Tambahkan metode untuk memperbarui data foto di server
        updateServerData(index, data) {
        const karyawanId = this.items[index].id_karyawan;
        axios.put(`http://localhost:3000/staff/${karyawanId}`, data)
          .then(() => {
            console.log('Upload File Staff Berhasil');
          })
          .catch(error => {
            console.error('Error updating data on the server:', error);
          });
      },
  
      fetchStaff(){
        axios.get('http://localhost:3000/staff').then(res => {
          this.items = res.data.payload.staff
          console.log(res)
        })
      },
      changePage() {
        // Memastikan currentPage berada dalam rentang yang valid
        if (this.currentPage < 1) {
          this.currentPage = 1;
        } else if (this.currentPage > this.totalPages()) {
          this.currentPage = this.totalPages();
        }
      },
      totalPages() {
        // Menghitung jumlah total halaman
        return Math.ceil(this.items.length / this.itemsPerPage);
      },
      incrementPage() {
        if (this.currentPage < this.totalPages()) {
          this.currentPage++;
        }
      },
      decrementPage() {
        if (this.currentPage > 1) {
          this.currentPage--;
        }
      },
      editItem(item) {
        this.isEditing = true;
        this.editedItem.id = item.id;
        this.editedItem.nama_karyawan = item.nama_karyawan;
        this.editedItem.alamat = item.alamat;
        this.editedItem.id_karyawan = item.id_karyawan;
        this.editedItem.jabatan = item.jabatan;
      },
      saveEdit() {
        axios
          .put(`http://localhost:3000/staff/${this.editedItem.id_karyawan}`, this.editedItem)
          .then(response => {
          console.log('Item berhasil diperbarui:', response.data);
          alert('Data staff berhasil diupdate!');
          // alert('Data staf berhasil diupdate!');
          this.fetchStaff();
          this.isEditing = false;
          // Reload halaman setelah berhasil menyimpan perubahan
          window.location.reload();
        })
          .catch(error => {
          console.error('Error saat menyimpan perubahan:', error);
        });
      },
      cancelEdit() {
        this.isEditing = false;
      },
      showDeleteConfirmation(item) {
      const confirmation = window.confirm(`Apakah Anda yakin ingin menghapus staf dengan id "${item}" ?`);
        if (confirmation) {
          axios
          .delete(`http://localhost:3000/staff/${item}`)
          .then(response => {
          console.log('Staf berhasil dihapus:', response.data);
          alert('Data staf berhasil dihapus!');
          this.fetchStaff();
          // Reload halaman setelah berhasil menyimpan perubahan
          window.location.reload();
          })
          .catch(error => {
          console.error(error);
          });
        }
      },
    },
    mounted(){
      this.fetchStaff();
    },
    computed: {
      // Filter the items based on the search term
      filteredItems() {
        const searchTerm = this.search.toLowerCase();
        return this.items.filter(item => {
          return (
            item.nama_karyawan.toLowerCase().includes(searchTerm) ||
            item.alamat.toLowerCase().includes(searchTerm) ||
            item.jabatan.toLowerCase().includes(searchTerm)
          );
        });
      },
    },
  };
  </script>