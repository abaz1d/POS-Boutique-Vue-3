<template>
  <div class="intro-y flex flex-col sm:flex-row items-center mt-8">
    <h2 class="text-lg font-medium mr-auto">Pelanggan</h2>
    <div class="w-full sm:w-auto flex mt-4 sm:mt-0">
      <button class="btn btn-primary shadow-md mb-3 mr-2 pr-5" @click="modal_utama = true">
        <PlusIcon class="w-4 h-4 mr-2" />
        <p class="hidden xl:block mr-1">Pelanggan</p> Baru
      </button>
      <!-- BEGIN: Modal Content -->
      <Modal backdrop="static" :show="modal_utama" @hidden="modal_utama = false">
        <ModalHeader>
          <h2 class="font-medium text-base mr-auto">
            <p class="mx-auto" v-if="isEdit">Edit Pelanggan {{ id_pelanggan }}</p>
            <p class="mx-auto" v-else>Tambah Pelanggan</p>
          </h2>
        </ModalHeader>
        <ModalBody class="grid grid-cols-12 gap-4 gap-y-3">
          <form @submit.prevent="isEdit ? updatePelanggan() : addPelanggan()" id="pelangganForm" class="col-span-12">
            <div class="col-span-12 mb-5">
              <label for="pos-form-1" class="form-label mb-1">Nama Pelanggan</label>
              <input id="pos-form-1" type="text" class="form-control flex-1" placeholder="Masukan Nama Pelanggan"
                v-model="nama_pelanggan" required />
            </div>
            <div class="col-span-12 mb-5">
              <label for="pos-form-5" class="form-label mb-1">Alamat Pelanggan</label>
              <textarea id="pos-form-5" class="form-control" placeholder="Masukan Alamat Pelanggan"
                v-model="alamat_pelanggan" required />
            </div>
            <div class="col-span-12 mb-5">
              <label for="pos-form-1" class="form-label mb-1">Telepon Pelanggan</label>
              <input id="pos-form-1" type="number" class="form-control flex-1" placeholder="Masukan Telepon Pelanggan"
                v-model="kontak_pelanggan" required />
            </div>
          </form>
        </ModalBody>
        <ModalFooter class="text-right">
          <button type="button"
            @click="modal_utama = false; id_pelanggan = ''; nama_pelanggan = ''; alamat_pelanggan = ''; kontak_pelanggan = ''; isEdit = false;"
            class="btn btn-outline-secondary w-32 mr-1">
            Cancel
          </button>
          <button type="submit" form="pelangganForm" class="btn btn-primary w-32">
            Simpan
          </button>
        </ModalFooter>
      </Modal>
      <a href="" class="ml-auto sm:ml-0 btn px-2 h-10 box flex items-center text-primary">
        <RefreshCcwIcon class="w-4 h-4 sm:mr-3 sm:m-0 m-2" /><p class="sm:block hidden">Reload Data</p> 
      </a>
    </div>
  </div>
  <!-- BEGIN: HTML Table Data -->
  <div class="intro-y box p-5 mt-5">
    <div class="flex flex-col sm:flex-row sm:items-end xl:items-start">
      <form id="tabulator-html-filter-form" class="xl:flex sm:mr-auto">
        <div class="sm:flex items-center sm:mr-4">
          <label class="w-12 flex-none xl:w-auto xl:flex-initial mr-2">Field</label>
          <select id="tabulator-html-filter-field" v-model="filter.field"
            class="form-select w-full sm:w-32 2xl:w-full mt-2 sm:mt-0 sm:w-auto">
            <option value="id_pelanggan">ID Pelanggan</option>
            <option value="nama_pelanggan">Nama Pelanggan</option>
            <option value="alamat_pelanggan">Alamat Pelanggan</option>
            <option value="kontak_pelanggan">Telepon Pelanggan</option>
          </select>
        </div>
        <div class="sm:flex items-center sm:mr-4 mt-2 xl:mt-0">
          <label class="w-12 flex-none xl:w-auto xl:flex-initial mr-2">Type</label>
          <select id="tabulator-html-filter-type" v-model="filter.type"
            class="form-select w-full mt-2 sm:mt-0 sm:w-auto">
            <option value="like" selected>like</option>
            <option value="=">=</option>
            <option value="<">&lt;</option>
            <option value="<=">&lt;=</option>
            <option value=">">></option>
            <option value=">=">>=</option>
            <option value="!=">!=</option>
          </select>
        </div>
        <div class="sm:flex items-center sm:mr-4 mt-2 xl:mt-0">
          <label class="w-12 flex-none xl:w-auto xl:flex-initial mr-2">Value</label>
          <input id="tabulator-html-filter-value" v-model="filter.value" type="text"
            class="form-control sm:w-40 2xl:w-full mt-2 sm:mt-0" placeholder="Search..." />
        </div>
        <div class="mt-2 xl:mt-0">
          <button id="tabulator-html-filter-go" type="button" class="btn btn-primary w-full sm:w-16" @click="onFilter">
            Go
          </button>
          <button id="tabulator-html-filter-reset" type="button"
            class="btn btn-secondary w-full sm:w-16 mt-2 sm:mt-0 sm:ml-1" @click="onResetFilter">
            Reset
          </button>
        </div>
      </form>
      <div class="flex mt-5 sm:mt-0">
        <button id="tabulator-print" class="btn btn-outline-secondary w-1/2 sm:w-auto mr-2" @click="onPrint">
          <PrinterIcon class="w-4 h-4 mr-2" /> Print
        </button>
        <Dropdown class="w-1/2 sm:w-auto">
          <DropdownToggle class="btn btn-outline-secondary w-full sm:w-auto">
            <FileTextIcon class="w-4 h-4 mr-2" /> Export
            <ChevronDownIcon class="w-4 h-4 ml-auto sm:ml-2" />
          </DropdownToggle>
          <DropdownMenu class="w-40">
            <DropdownContent>
              <DropdownItem @click="onExportCsv">
                <FileTextIcon class="w-4 h-4 mr-2" /> Export CSV
              </DropdownItem>
              <!-- <DropdownItem @click="onExportJson">
                <FileTextIcon class="w-4 h-4 mr-2" /> Export JSON
              </DropdownItem> -->
              <DropdownItem @click="onExportXlsx">
                <FileTextIcon class="w-4 h-4 mr-2" /> Export XLSX
              </DropdownItem>
              <!-- <DropdownItem @click="onExportHtml">
                <FileTextIcon class="w-4 h-4 mr-2" /> Export HTML
              </DropdownItem> -->
            </DropdownContent>
          </DropdownMenu>
        </Dropdown>
      </div>
    </div>
    <div class="overflow-x-auto scrollbar-hidden">
      <div id="tabulator" ref="tableRef" class="mt-5 table-report table-report--tabulator"></div>
    </div>
  </div>

  <Modal :show="deleteConfirmationModal" @hidden="deleteConfirmationModal = false">
    <ModalBody class="p-0">
      <div class="p-5 text-center">
        <XCircleIcon class="w-16 h-16 text-danger mx-auto mt-3" />
        <div class="text-3xl mt-5">Apakah Anda Yakin ?</div>
        <div class="text-slate-500 mt-2">
          Anda yakin ingin menghapus data <b>{{ nama_pelanggan }}</b> ? <br />Data
          yang telah dihapus tidak bisa kembali.
        </div>
      </div>
      <div class="px-5 pb-8 text-center">
        <button type="button" @click="deleteConfirmationModal = false" class="btn btn-outline-secondary w-24 mr-1">
          Batal
        </button>
        <button type="button" class="btn btn-danger w-24" @click="
  (e) => {
    e.preventDefault();
    deletePelanggan(id_pelanggan);
  }
        ">
          Hapus
        </button>
      </div>
    </ModalBody>
  </Modal>
  <!-- END: HTML Table Data -->
</template>

<script>
import { usePelangganStore } from "../../stores/pelanggan";
// import PelangganList from "./PelangganList.vue";
import { ref, reactive } from "vue";
import xlsx from "xlsx";
import { createIcons, icons } from "lucide";
import { TabulatorFull as Tabulator } from 'tabulator-tables';
import dom from "@left4code/tw-starter/dist/js/dom";
import moment from "moment";

// const Pelanggan = usePelangganStore();
const modal_utama = ref(false);
const id_pelanggan = ref("");
const nama_pelanggan = ref("");
const alamat_pelanggan = ref("");
const kontak_pelanggan = ref();
const deleteConfirmationModal = ref(false);
const isEdit = ref(false);

// const tableRef = ref("");
const tabulator = ref();
const filter = reactive({
  field: "id_pelanggan",
  type: "like",
  value: "",
});



export default {
  setup() {
    const Pelanggan = usePelangganStore();
    return { Pelanggan, moment, };
  },
  // components: {
  //   PelangganList,
  // },
  data() {
    return {
      deleteConfirmationModal,
      id_pelanggan,
      nama_pelanggan,

      modal_utama,
      alamat_pelanggan,
      kontak_pelanggan,

      //tableRef,
      tabulator,
      filter,
      isEdit
    };
  },
  methods: {
    addPelanggan() {
      try {
        // console.log("addPelanggan", nama_pelanggan.value, alamat_pelanggan.value)
        this.Pelanggan.addItem(
          nama_pelanggan.value,
          alamat_pelanggan.value,
          kontak_pelanggan.value,
        ).then(() => {
          this.modal_utama = false;
          this.initTabulator();
        })
        nama_pelanggan.value = "";
        alamat_pelanggan.value = "";
        kontak_pelanggan.value = "";

      } catch (error) {
        alert("Gagal Tambah Data" + error);
      }
    },
    updatePelanggan() {
      try {
        this.Pelanggan.updateItem({
          id_pelanggan: this.id_pelanggan,
          nama_pelanggan: this.nama_pelanggan,
          alamat_pelanggan: this.alamat_pelanggan,
          kontak_pelanggan: this.kontak_pelanggan,
        }).then(() => {
          this.initTabulator();
          this.isEdit = false;
          this.modal_utama = false;
          this.id_pelanggan = ""
          this.nama_pelanggan = ""
          this.alamat_pelanggan = ""
          this.kontak_pelanggan = ""
        });
        //console.log("update", this.id_pelanggan, this.nama_pelanggan, this.alamat_pelanggan, this.kontak_pelanggan,

      } catch (error) {
        alert(`Gagal Update data ${id_pelanggan}` + error);
      }
    },
    deletePelanggan(id_pelanggan) {
      try {
        this.Pelanggan.removeItem(id_pelanggan).then(() => {
          this.initTabulator();
          this.deleteConfirmationModal = false;
          this.id_pelanggan = "";
          this.nama_pelanggan = "";
        });
      } catch (error) {
        alert(`Gagal Delete Pelanggan ${id_pelanggan}` + error);
      }
    },

    initTabulator() {
      this.tabulator = new Tabulator(this.$refs.tableRef, {
        // ajaxURL: "https://dummy-data.left4code.com",
        // ajaxFiltering: true,
        // ajaxSorting: true,
        //ajaxLoaderLoading:"<span>Loading Data</span>",
        printAsHtml: true,
        printStyled: true,
        printHeader: `<h1 class='text-2xl p-2 m-2 text-center border-y-2 border-black'>Tabel Pelanggan<h1>`,
        printFooter: `<h2 class='p-2 m-2 text-center mt-4'>${moment(Date.now()).format("DD MMM YYYY HH:SS")}<h2>`,
        data: this.Pelanggan.items,
        pagination: "remote",
        paginationSize: 10,
        paginationSizeSelector: [10, 20, 30, 40, 50],
        layout: "fitColumns",
        responsiveLayout: "collapse",
        placeholder: "Tida ada Data di temukan",
        columns: [
          {
            formatter: "responsiveCollapse",
            width: 40,
            minWidth: 30,
            hozAlign: "center",
            resizable: false,
            headerSort: false,
          },

          // For HTML table
          {
            title: "ID SUPPLIER",
            // minWidth: 200,
            maxWidth: 145,
            responsive: 0,
            field: "id_pelanggan",
            vertAlign: "middle",
            print: false,
            download: false,
            formatter(cell) {
              return `<div>
                <div class="font-medium whitespace-nowrap">${cell.getData().id_pelanggan
                }</div>
              </div>`;
            },
          },
          {
            title: "NAMA SUPPLIER",
            headerHozAlign: "center",
            minWidth: 200,
            field: "nama_pelanggan",
            hozAlign: "center",
            vertAlign: "middle",
            print: false,
            editor: "input",
            editable: false, cellDblClick: function (e, cell) {
              cell.edit(true);
            },
            download: false,
            formatter(cell) {
              return `<div>
                <div class="font-medium whitespace-nowrap">${cell.getData().nama_pelanggan
                }</div>
              </div>`;
            },
          },
          {
            title: "ALAMAT",
            minWidth: 500,
            headerHozAlign: "center",
            field: "alamat_pelanggan",
            hozAlign: "center",
            vertAlign: "middle",
            print: false,
            editor: "textarea",
            editable: false, cellDblClick: function (e, cell) {
              cell.edit(true);
            },
            download: false,
            formatter(cell) {
              return `<div>
                <div class="font-medium whitespace-nowrap">${cell.getData().alamat_pelanggan
                }</div>
              </div>`;
            },
          },
          {
            title: "TELEPON",
            headerHozAlign: "center",
            minWidth: 200,
            field: "kontak_pelanggan",
            hozAlign: "center",
            vertAlign: "middle",
            print: false,
            editor: "input",
            editable: false, cellDblClick: function (e, cell) {
              cell.edit(true);
            },
            download: false,
            formatter(cell) {
              return `<div>
                <div class="font-medium whitespace-nowrap">${cell.getData().kontak_pelanggan
                }</div>
              </div>`;
            },
          },
          {
            title: "ACTIONS",
            headerHozAlign: "center",
            minWidth: 200,
            field: "actions",
            responsive: 1,
            hozAlign: "center",
            vertAlign: "middle",
            print: false,
            download: false,
            formatter(cell) {
              const a = dom(`<div class="flex lg:justify-center items-center">
                <a id="edit" class="flex items-center mr-3" href="javascript:;">
                  <i data-lucide="check-square" class="w-4 h-4 mr-1"></i> Edit
                </a>
                <a id="delete" class="flex items-center text-danger" href="javascript:;">
                  <i data-lucide="trash-2" class="w-4 h-4 mr-1"></i> Delete
                </a>
              </div>`);
              // const func = deleteConfirmationModal
              dom(a).on("click", "a", function (e) {
                // On click actions
                if (e.id === "edit") {
                  //alert("edit" + cell.getData().id_pelanggan);
                  id_pelanggan.value = cell.getData().id_pelanggan
                  nama_pelanggan.value = cell.getData().nama_pelanggan
                  alamat_pelanggan.value = cell.getData().alamat_pelanggan
                  kontak_pelanggan.value = cell.getData().kontak_pelanggan
                  isEdit.value = true
                  modal_utama.value = true
                } else {
                  id_pelanggan.value = cell.getData().id_pelanggan
                  nama_pelanggan.value = cell.getData().nama_pelanggan
                  deleteConfirmationModal.value = true
                  //console.log("hapus", id_pelanggan.value, nama_pelanggan.value)
                }
              });
              return a[0]

            },
          },

          // For print format
          {
            title: "ID SUPPLIER",
            field: "id_pelanggan",
            visible: false,
            print: true,
            download: true,
          },
          {
            title: "NAMA SUPPLIER",
            field: "nama_pelanggan",
            visible: false,
            print: true,
            download: true,
          },
          {
            title: "ALAMAT",
            field: "alamat_pelanggan",
            visible: false,
            print: true,
            download: true,
          },
          {
            title: "TELEPON",
            field: "kontak_pelanggan",
            visible: false,
            print: true,
            download: true,
          },
        ],
      });
      this.tabulator.on("renderComplete", function () {
        //subTable.redraw();
        createIcons({
          icons,
          "stroke-width": 1.5,
          nameAttr: "data-lucide",

        });
      });
      this.tabulator.on("cellEdited", function (cell) {
        //cell - cell component
        id_pelanggan.value = cell.getData().id_pelanggan
        nama_pelanggan.value = cell.getData().nama_pelanggan
        alamat_pelanggan.value = cell.getData().alamat_pelanggan
        kontak_pelanggan.value = cell.getData().kontak_pelanggan
        isEdit.value = true
        modal_utama.value = true
        // console.log("aku cengar cengir", cell.getData(), this.Pelanggan.items)
      });
    },
    reInitOnResizeWindow() {
      window.addEventListener("resize", () => {
        this.tabulator.redraw();
        createIcons({
          icons,
          "stroke-width": 1.5,
          nameAttr: "data-lucide",
        });
      });
    },
    onFilter() {
      this.tabulator.setFilter(this.filter.field, this.filter.type, this.filter.value);
    },

    onResetFilter() {
      this.filter.field = "id_pelanggan";
      this.filter.type = "like";
      this.filter.value = "";
      this.onFilter();
    },

    // Export
    onExportCsv() {
      this.tabulator.download("csv", "data.csv");
    },

    onExportJson() {
      this.tabulator.download("json", "data.json");
    },

    onExportXlsx() {
      const win = window;
      win.XLSX = xlsx;
      this.tabulator.download("xlsx", "data.xlsx", {
        sheetName: "Data Pelanggan",
      });
    },

    onExportHtml() {
      this.tabulator.download("html", "data.html", {
        style: true,
      });
    },

    // Print
    onPrint() {
      this.tabulator.print();
    },

  },
  beforeCreate() {
    this.Pelanggan.readItem().then(() => {
      this.initTabulator();
      this.reInitOnResizeWindow();
    }).catch((error) => {
      alert(error)
    });
    // this.pelanggans = this.Pelanggan.items
  },
  // mounted() {
  //   this.initTabulator();
  //   this.reInitOnResizeWindow();
  // }
};
</script>
