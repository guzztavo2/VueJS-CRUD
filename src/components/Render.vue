<template>
  <div class="container">
    <div class="d-md-flex flex-row flex-wrap justify-content-center align-items-center bg-light p-2"
      style="height: 120px">
      <b-button :block="true" ref="btnModalGerarInfo" squared variant="dark"
        @click="abrirModal('modal-gerarInformacoes', 'btnModalGerarInfo')">Gerar Informações</b-button>

      <b-button class="w-100 d-block" squared variant="warning" ref="adicionarInformacaoBtn"
        @click="abrirModal('modal-limparInformacoes', '')">Limpar todas Informações</b-button>

      <Modal typeModal="limparModal">
        <template #headerModal>
          <span style="color:black" class="d-block bg-warning radius-20 w-100 p-2">Limpar Informações ❗</span>
        </template>
        <template #bodyModal>
          <span class="d-block bg-danger w-100 p-4" style="text-align:center; color:white"> Ao clicar em sim, você
            estará concordando em limpar a tabela inteira de dados. Você tem certeza de que quer fazer isso?</span>
        </template>
        <template #footerModal>
          <div class="w-100 d-flex flex-row">
            <b-button class="w-50 m-1" variant="warning" @click="limparRegistros('modal-limparInformacoes')">Sim, quero
              limpar todos os dados da tabela.</b-button>
            <b-button class="w-50 m-1" variant="dark" ref="cancelarbtn"
              @click="fecharModal('modal-limparInformacoes', '')">Cancelar</b-button>
          </div>

        </template>



      </Modal>
      <div class="d-flex flex-row w-100 bg-black" style="justify-content:center">
        <b-button squared variant="primary" ref="adicionarInformacaoBtn"
          @click="abrirModal('modal-adicionarInformacao', 'adicionarInformacaoBtn')">Registrar Informações</b-button>



        <div v-if="items.length === 0">
          <b-button squared variant="danger" ref="deletarInfoBtn"
            @click="abrirModal('modal-Custom', 'deletarInfoBtn')">Deletar Informações</b-button>
          <Modal typeModal="customModal">
            <template #titleModal>
              <h5 class="bg-danger w-100 h-100 p-3" style="color:white;">Erro ⚠</h5>
            </template>

            <template #bodyModal>Não há itens para selecionar e deleta-los, clique aqui para
              <b-button :inline="true" squared variant="warning" @click="abrirModal('modal-gerarInformacoes', '')">Gerar
                Informações</b-button>, e escolha a
              quantidade para gerar informações.</template>

          </Modal>

        </div>

        <div v-else-if="items.length > 0 && selected.length === 0">
          <b-button squared variant="danger" ref="deletarInfoBtn"
            @click="abrirModal('modal-Custom', 'deletarInfoBtn')">Deletar Informações</b-button>
          <Modal typeModal="customModal">
            <template #titleModal>
              <h5 class="bg-danger w-100 h-100 p-3" style="color:white;">Erro ⚠</h5>
            </template>

            <template #bodyModal>Escolha os itens antes de deleta-los.</template>

          </Modal>
        </div>

        <div v-else-if="items.length > 0 && selected.length > 0">
          <b-button squared variant="danger" @click="deletarInfo">Deletar Informações</b-button>

        </div>
        <Modal typeModal="gerarInformacoes"></Modal>

      </div>
      <Modal typeModal="adicionarModal">
        <template #headerModal>
          <span style="color:white" class="d-block bg-dark w-100 p-2">Adicionar informação:
            {{ novaInformacao.informacao }}</span>
        </template>
        <template #bodyModal>
          <b-form-group id="labelInput" label="Informação" label-for="informacao-input"
            :valid-feedback="inputalert.message" :invalid-feedback="inputalert.message">
            <b-form-input :state="inputalert.typeMessage !== undefined ? inputalert.typeMessage : null"
              @keyup="inputRegistrarInformacaoAction" id="informacao-input" v-model="novaInformacao.informacao"
              required></b-form-input>
          </b-form-group>
        </template>
        <template #footerModal>
          <div class="w-100 d-flex flex-row">
            <b-button class="w-50 m-1" variant="dark"
              @click="salvarInformacaoRegistrada('modal-adicionarInformacao')">Salvar</b-button>
            <b-button class="w-50 m-1" variant="danger" ref="cancelarbtn"
              @click="fecharModal('modal-adicionarInformacao', 'cancelarbtn')">Cancelar</b-button>
          </div>

        </template>
      </Modal>
    </div>

    <div class="d-md-flex flex-row flex-wrap justify-content-center align-items-center bg-light p-2">

      <b-table :head-variant="'dark'" :table-variant="'light'" :select-mode="'multi'" ref="selectableTable" selectable
        @row-selected="onRowSelected" striped hover :items="items" :fields="headerTableList" :sort-by.sync="sortBy"
        :sort-desc.sync="sortDesc" :per-page="perPage" responsive sticky-header='500px' style="max-width: 100%;"
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
          <div class="w-100 d-flex flex-wrap">
            <b-button inline class="w-50" style="max-width: 100%;min-width:90px" :ref="'btnEditar-' + row.item.id"
              v-bind:infoID="row.item.id" variant="warning"
              @click="abrirModal('modal-editarInformacao', 'btnEditar-' + row.item.id)">Editar</b-button>

            <b-button inline class="w-50" style="max-width: 100%; min-width:90px" variant="danger"
              @click="deletarItem(row.item)">Deletar</b-button>
          </div>
        </template>

        <template #cell(dataAtualizacao)="row">
          {{ row.value.toLocaleString() }}
        </template>
        <template #cell(dataCriacao)="row">
          {{ row.value.toLocaleString() }}
        </template>

      </b-table>
    </div>
    <div v-if="items.length > 0 && informacaoEditada !== undefined">
      <Modal typeModal="editarModal">
        <template #headerModal>
          <div class="bg-dark w-100 h-100 p-3"><span style="color:white;">Editar Informação:
              {{ items[informacaoEditada.id - 1].informacao }} ({{ informacaoEditada.id }})</span></div>
        </template>
        <template #bodyModal>
          <b-form-group id="labelInput" label="Identificação" label-for="informacao-input">
            <b-form-input id="informacao-input" disabled v-model="informacaoEditada.id" required></b-form-input>
          </b-form-group>
          <b-form-group id="labelInput" label="Informação" label-for="informacao-input"
            :valid-feedback="inputalert.message" :invalid-feedback="inputalert.message">
            <b-form-input :state="inputalert.typeMessage !== undefined ? inputalert.typeMessage : null"
              @keyup="inputRegistrarInformacaoAction" id="informacao-input" v-model="informacaoEditada.informacao"
              required></b-form-input>
          </b-form-group>
          <b-form-group id="labelInput" label="Data de criação" label-for="informacao-input">
            <b-form-input id="informacao-input" v-b-tooltip.hover
              title="Essa informação só é registrada, quando você registra a informação." disabled
              :value="informacaoEditada.dataCriacao !== undefined ? informacaoEditada.dataCriacao.toLocaleString() : ''"
              required></b-form-input>
          </b-form-group>
          <b-form-group id="labelInput" label="Data de Atualização" label-for="informacao-input">
            <b-form-input v-b-tooltip.hover
              title="Essa informação só é registrada, quando você edita e salva a informação." id="informacao-input"
              disabled
              :value="informacaoEditada.dataAtualizacao !== undefined ? informacaoEditada.dataAtualizacao.toLocaleString() : ''"
              required></b-form-input>
          </b-form-group>
        </template>
        <template #footerModal>
          <div class="w-100 d-flex flex-row">
            <b-button class="w-50 m-1" variant="dark"
              @click="salvarInformacaoEditada('modal-editarInformacao')">Salvar</b-button>
            <b-button class="w-50 m-1" variant="danger" ref="cancelarbtn"
              @click="fecharModal('modal-editarInformacao', 'cancelarbtn')">Cancelar</b-button>
          </div>
        </template>

      </Modal>
    </div>
    <div class="d-lg-flex w-100 p-0">

      <b-pagination size="lg" class="p-0 justify-content-center w-100" variant="warning" v-model="currentPage"
        :total-rows="rows" :per-page="perPage"></b-pagination>
    </div>
  </div>
</template>

<script lang="ts">
import Informacao from '../model/Model.Informacao.vue';
import Modal from './Modal-box.vue';

export interface inputAlert {
  message?: string,
  typeMessage?: boolean
}

export default {
  name: "Render",
  created() {
    (this as any).$root.$refs.Render = this;
    if ((this as any).items.length > 0)
      (this as any).informacaoEditada = (this as any).items[0];
  },
  computed: {
    rows() {
      return (this as any).items.length
    },

  },
  data() {
    return {
      inputalert: { message: '', typeMessage: undefined } as inputAlert,
      perPage: 10,
      currentPage: 1,
      sortBy: 'ID',
      sortDesc: false,
      headerTableList: ['Selecione', { key: 'id', label: 'Identificador', sortable: true },
        { key: 'informacao', label: 'Informação', sortable: true }, { key: 'dataCriacao', label: 'Data de Criação', sortable: true }, { key: 'dataAtualizacao', label: 'Data de Atualização', sortable: true }, { key: 'editarDeletar', label: "Editar / Deletar" }],
      items: Informacao.gerarInformacoes(10) as Array<Informacao>,
      selected: [],
      informacaoEditada: undefined as Informacao | undefined,
      novaInformacao: new Informacao(undefined)
    };
  },
  props: {},
  components: {
    Modal
  },
  methods: {
    limparRegistros(idModal: string) {
      (this as any).items = [];
      (this as any).fecharModal(idModal, '');
    },
    salvarInformacaoRegistrada(idModal: string) {
      let informacaoNova: Informacao = new Informacao((this as any).novaInformacao.informacao);
      (this as any).novaInformacao.informacao = '';
      (this as any).items.push(informacaoNova);
      (this as any).items = Informacao.reorganizarIDs((this as any).items);
      (this as any).fecharModal(idModal, '');
    },
    inputRegistrarInformacaoAction(event: Event) {
      let quantidadeMin = 3;
      let targetLength = (event.target as any).value.length;
      if (targetLength < quantidadeMin) {
        (this as any).inputalert = { message: 'A informação tem de ser válida, se você clicar em salvar, nada será salvo. Faltam: ' + (quantidadeMin - targetLength) + ' caracteres. ', typeMessage: false }
      } else {
        (this as any).inputalert = { message: 'Agora está tudo certo! Clique em salvar. ', typeMessage: true }

      }
    },
    abrirModal(idModal: string, btnRef: string) {
      if (idModal === 'modal-editarInformacao') {
        let idInformacao: number = (this as any).$refs[btnRef].getAttribute('infoid');
        (this as any).informacaoEditada = { ... (this as any).items[idInformacao - 1] };
        (this as any).inputalert = { message: 'Se você não editar as informações, ela não será atualizada.', typeMessage: undefined }
      }
      (this as any).$root.$emit('bv::show::modal', idModal, '#' + btnRef);

    },
    fecharModal(idModal: string, btnRef: string) {
      (this as any).inputalert = { message: '', typeMessage: undefined };

      (this as any).$root.$emit('bv::hide::modal', idModal, '#' + btnRef);

    },
    salvarInformacaoEditada(idModal: string) {
      (this as any).items[(this as any).informacaoEditada.id - 1].atualizarInformacao((this as any).informacaoEditada.informacao);
      (this as any).$refs.selectableTable.refresh();
      (this as any).fecharModal(idModal, '');
    },
    onRowSelected(item: any) {
      (this as any).selected = item
    },
    add(items: Array<Informacao>): void {
      items.forEach((item) => {

        (this as any).$data.items.push(item);
        (this as any).$data.items = Informacao.reorganizarIDs((this as any).$data.items);
        (this as any).$data.informacaoEditada = (this as any).$data.items[0];
      })
    },
    deletarInfo(event: any) {
      if ((this as any).selected.length > 0) {
        let selected = (this as any).selected, This = (this as any);

        selected.forEach(function (item1: any) {
          This.deletarItem(item1);
        })
      }
    },
    deletarItem(informacaoDeletada: Informacao) {
      (this as any).items = (this as any).items.filter((item: Informacao) => item !== informacaoDeletada);
    },
    selectAllRows() {

      if ((this as any).selected.length == 0)
        (this as any).$refs.selectableTable.selectAllRows();


      else
        (this as any).clearSelected();


      return;

    },
    clearSelected() {
      if ((this as any).$refs.selectableTable !== undefined) {
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


  }

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
