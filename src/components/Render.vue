<template>
  <div class="container">
    <div class="d-md-flex flex-row flex-wrap justify-content-center align-items-center bg-light p-2"
      style="height: 120px">
      <b-button :block="true" ref="btnModalGerarInfo" squared variant="dark" @click="abrirModal('modal-gerarInformacoes', 'btnModalGerarInfo')">Gerar Informações</b-button>
      <div class="d-flex flex-row flex-wrap justify-content-between" style="width:50%">
        <b-button squared variant="primary" v-b-modal.modal-gerarInformacoes>Registrar Informações</b-button>
        <div v-if="selected.length === 0">
          <b-button squared variant="danger" ref="deletarInfoBtn" @click="abrirModal('modal-Custom', 'deletarInfoBtn')">Deletar Informações</b-button>
          <div v-if="items.length === 0">
            <Modal typeModal="customModal">
              <template #titleModal>
                <h5 class="bg-danger w-100 h-100 p-3" style="color:white;">Erro ⚠</h5>
              </template>

              <template #bodyModal>Não há itens para selecionar e deleta-los, clique aqui para 
              <b-button :inline="true" squared variant="warning" @click="abrirModal('modal-gerarInformacoes', '')" >Gerar Informações</b-button>, e escolha a
                quantidade para gerar informações.</template>

            </Modal>
          </div>
          <div v-else="items.lenght > 0">
            <Modal typeModal="customModal">
              <template #titleModal>
                <h5 class="bg-danger w-100 h-100 p-3" style="color:white;">Erro ⚠</h5>
              </template>

              <template #bodyModal>Escolha os itens antes de deleta-los.</template>

            </Modal>
          </div>
        </div>
        <div v-else>
          <b-button squared variant="danger" @click="deletarInfo">Deletar Informações</b-button>

        </div>
      </div>
      <Modal typeModal="gerarInformacoes"></Modal>

    </div>

    <div class="d-md-flex flex-row flex-wrap justify-content-center align-items-center bg-light p-2">

      <b-table :head-variant="'dark'" :table-variant="'light'" :select-mode="'multi'" :fixed="true"
        ref="selectableTable" selectable @row-selected="onRowSelected" striped hover :items="items"
        :fields="headerTableList" :sort-by.sync="sortBy" :sort-desc.sync="sortDesc" :per-page="perPage"
        :current-page="currentPage">
        <template #head(Selecione)>
          <span block class="p-2" style="user-select:none; cursor:pointer" @click="selectAllRows">Selecionar</span>
        </template>
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
        

        <template #cell(editarDeletar)="row">
          <b-button inline class="w-50" :ref="'btnEditar-'+row.item.id" v-bind:infoID="row.item.id" variant="danger" @click="abrirModal('modal-editarInformacao', 'btnEditar-'+row.item.id)" >Editar</b-button>

          <b-button inline class="w-50" variant="warning">Deletar</b-button>

        </template>

        <template #cell(dataAtualizacao)="row">
          {{ row.value.toLocaleString() }}
        </template>
        <template #cell(dataCriacao)="row">
          {{ row.value.toLocaleString() }}
        </template>

      </b-table>
    </div>
    <div v-if="items.length > 0 && informacaoEditada !== null">
    <Modal ref="editarModalTAG" typeModal="editarModal">
      <template #headerModal> <div class="bg-dark w-100 h-100 p-3"><span style="color:white;">Editar Informação: {{items[informacaoEditada.id - 1].informacao}} ({{informacaoEditada.id}})</span></div> </template>
        <template #bodyModal>
          <b-form-group id="labelInput" label="Identificação" label-for="informacao-input">
            <b-form-input id="informacao-input" disabled v-model="informacaoEditada.id"
               required></b-form-input>
          </b-form-group>
          <b-form-group id="labelInput" label="Informação" label-for="informacao-input">
            <b-form-input id="informacao-input" v-model="informacaoEditada.informacao"
               required></b-form-input>
          </b-form-group>
          <b-form-group  v-b-tooltip.hover title="Tooltip directive content" id="labelInput" label="Data de criação" label-for="informacao-input">
            <b-form-input id="informacao-input" disabled :value="informacaoEditada.dataCriacao !== undefined ? informacaoEditada.dataCriacao.toLocaleString() : ''"
               required></b-form-input>
          </b-form-group>
          <b-form-group id="labelInput" label="Data de Atualização" label-for="informacao-input">
            <b-form-input  v-b-tooltip.hover title="Tooltip directive content" id="informacao-input" disabled :value="informacaoEditada.dataAtualizacao !== undefined ? informacaoEditada.dataAtualizacao.toLocaleString() : ''"
           
               required></b-form-input>
          </b-form-group>
        </template>
        <template #footerModal>
            <div class="w-100 d-flex flex-row">
          <b-button  class="w-50 m-1" variant="dark" @click="" >Salvar</b-button>
          <b-button class="w-50 m-1" variant="danger" @click="" >Cancelar</b-button>
        </div>
        </template>
      
      </Modal>
    </div>
    <div class="d-lg-flex justify-content-center p-2">

      <b-pagination size="lg" class="p-1" variant="warning" v-model="currentPage" :total-rows="rows"
        :per-page="perPage"></b-pagination>
    </div>
  </div>
</template>

<script lang="ts">
import { Informacao } from '../model/Model.Informacao.vue';
import Modal from './Modal-box.vue';
export default {
  name: "Render",
  created() {
    this.$root.$refs.Render = this;
  },
  computed: {
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
        { key: 'informacao', label: 'Informação', sortable: true }, { key: 'dataCriacao', label: 'Data de Criação', sortable: true }, { key: 'dataAtualizacao', label: 'Data de Atualização', sortable: true }, { key: 'editarDeletar', label: "Editar / Deletar" }],
      items: [] as Array<Informacao>,
      selected: [],
      informacaoEditada: null as Informacao | null
    };
  },
  props: {},
  components: {
    Modal
  },
  methods: {
    abrirModal(idModal:string, btnRef:string){
      let idInformacao:number = (this as any).$refs[btnRef].getAttribute('infoid');
        this.informacaoEditada = Object.assign({}, this.items[idInformacao - 1])
     
      this.$root.$emit('bv::show::modal', idModal, '#' + btnRef);

    },
    fecharModal(idModal: string,btnRef:string) {
      this.$root.$emit('bv::show::modal', idModal, '#' + btnRef);

    },
    onRowSelected(item: any) {
      this.selected = item
    },
    add(items: Array<Informacao>): void {
      items.forEach((item) => {

        this.$data.items.push(item);
        this.$data.items = Informacao.reorganizarIDs(this.$data.items);
        this.$data.informacaoEditada = this.$data.items[0];
      })
    },
    deletarInfo(event: any) {
      if (this.selected.length > 0) {
        let selected = this.selected,
          items = this.items,
          resultado: Informacao[] = [];

        selected.forEach(function (item1) {
          items = items.filter(item => item !== item1)

        })
        this.items = items;
      }


    },
    selectAllRows() {
      if (this.$refs.selectableTable !== undefined) {
        if (this.selected.length !== 10)
          (this as any).$refs.selectableTable.selectAllRows();
        else
          this.clearSelected();
        return;
      }
    },
    clearSelected() {
      if (this.$refs.selectableTable !== undefined) {
        (this as any).$refs.selectableTable.clearSelected()
      }
    }
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
