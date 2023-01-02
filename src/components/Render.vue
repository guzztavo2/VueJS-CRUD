<template>
  <div class="container">
    <div class="d-md-flex flex-row flex-wrap justify-content-center align-items-center bg-light p-2"
      style="height: 120px">
      <b-button :block="true" squared variant="dark" v-b-modal.modal-gerarInformacoes>Gerar Informações</b-button>
      <div class="d-flex flex-row flex-wrap justify-content-between" style="width:50%">
        <b-button squared variant="primary" v-b-modal.modal-gerarInformacoes>Registrar Informações</b-button>
        <div v-if="selected.length === 0">
          <b-button squared variant="danger" v-b-modal.modal-Custom>Deletar Informações</b-button>
          <Modal typeModal="customModal" titleModal="Erro" bodyModal="FAIL"/>

        </div>
        <div v-else>
          <b-button squared variant="danger" @click="deletarInfo">Deletar Informações</b-button>

        </div>
      </div>
    </div>
    <Modal titleModal="undefined" bodyModal="undefined" typeModal="gerarInformacoes"/>

    <div class="d-md-flex flex-row flex-wrap justify-content-center align-items-center bg-light p-2">

    <b-table :head-variant="'dark'" :table-variant="'light'" :select-mode="'multi'" :fixed="true"
      ref="selectableTable" selectable @row-selected="onRowSelected" striped hover :items="items"
      :fields="headerTableList" :sort-by.sync="sortBy" :sort-desc.sync="sortDesc"
      :per-page="perPage"
      :current-page="currentPage"
      >
      <template #cell(Selecione)="{ rowSelected }">
        <template v-if="rowSelected">
          <span aria-hidden="true">&check;</span>
          <span class="sr-only">Selected</span>
        </template>
        <template v-else>
          <span aria-hidden="true">&nbsp;</span>
          <span class="sr-only">Not selected</span>
        </template>
      </template>


      <template #cell(dataCriacao)="row">
        {{ row.value.toLocaleString() }}
      </template>

    </b-table>
    </div>
    <div class="d-lg-flex justify-content-center p-2">
    
    <b-pagination
 
      size="lg"
      class="p-1"
      variant="warning"
      v-model="currentPage"
      :total-rows="rows"
      :per-page="perPage"
    ></b-pagination>
  </div>
  </div>
</template>

<script lang="ts">
import type { Informacao } from '../model/Model.Informacao.vue';
import Modal from './Modal-box.vue';
export default {
  name: "Render",
  created() {
    this.$root.$refs.Render = this;
   },
   computed:{
    rows() {
        return this.items.length
      },

   },
  data() {
    return {
      perPage: 10,
      currentPage: 1,
      sortBy: 'ID',
      sortDesc: false,
      headerTableList: ['Selecione', { key: 'id', label: 'Identificador', sortable: true },
        { key: 'informacao', label: 'Informação', sortable: true }, { key: 'dataCriacao', label: 'Data de Criação', sortable: true }, { key: 'dataAtualizacao', label: 'Data de Atualização', sortable: true }, 'Editar/Deletar'],
      items: [] as Array<Informacao>,
      selected: []
    };
  },
  props: {},
  components:{
    Modal
  },
  methods: {
 
    onRowSelected(item: any) {
      this.selected = item
    },
    add(items:Array<Informacao>):void{
      items.forEach((item)=>{
        this.$data.items.push(item);
      })
    },
    deletarInfo(event:any){
      alert('a');
      
      let count = (this as any).$refs.selectableTable.selectedRows
      if(count.length === 0){
        (this as any).$refs.customModal.show()
        console.log();
              }
    }
    // selectAllRows() {
    //   this.$refs.selectableTable.selectAllRows()
    // },
    // clearSelected() {
    //   this.$refs.selectableTable.clearSelected()
    // },
    // selectThirdRow() {
    //   // Rows are indexed from 0, so the third row is index 2
    //   this.$refs.selectableTable.selectRow(2)
    // },
    // unselectThirdRow() {
    //   // Rows are indexed from 0, so the third row is index 2
    //   this.$refs.selectableTable.unselectRow(2)
    // }


  },

};
</script>

<style lang="css" scoped>

li.page-item.active button.page-link {
	z-index: 3;
	color: #fff;
	background-color: red !important;
	border-color: green !important;
}
html body div#app div.container div.d-md-block.bg-light.p-2 button.btn.btn-primary.btn-block.rounded-0 {
  margin: 1% 0 !important;
}
</style>
