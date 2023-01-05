<template>
  <div>
    <div v-if="typeModal === 'gerarInformacoes'">

      <b-modal @hidden="cancelarFunc('gerarInformacoes')" id="modal-gerarInformacoes" v-model="modalGerarInformacoes"
        title="Digite em números a quantidade de informações que você quer gerar:">

        <form ref="form" @submit.stop.prevent="handleSubmit('gerarInformacoes')">

          <b-form-group id="labelInput" label="Informação" label-for="informacao-input" :invalid-feedback="labelStr">
            <b-form-input id="informacao-input" @keyup="stateInputFunc('gerarInformacoes')" v-model="name"
              :state="nameState" required></b-form-input>
          </b-form-group>

        </form>
        <template #modal-footer="{}">
          <!-- Emulate built in modal footer ok and cancel button actions -->
          <b-button size="sm" variant="success" id="successBtn" @click="salvarInformacaoGerada">
            Gerar
          </b-button>
          <b-button size="sm" variant="danger" @click="cancelarFunc('gerarInformacoes')"> Cancelar </b-button>
        </template>
      </b-modal>
    </div>








    <div v-else-if="typeModal === 'customModal'">
      <b-modal no-stacking ok-only id="modal-Custom" v-model="modalCustom" ref="modalCustomInformacoes">
        <template #modal-header>

          <slot name="titleModal"> </slot>

        </template>
        <slot name="bodyModal"> </slot>
      </b-modal>
      <template name="footerModal">
      
      </template>
    </div>







    <div v-else-if="typeModal === 'editarModal'">
      <b-modal @hidden="cancelarFunc('editarInformacao')" id="modal-editarInformacao" :title="'Editar informação:'"
        v-model="modalEditarInformacao">
        <template #modal-header>
          <slot name="headerModal">
          </slot>
        </template>
        <slot name="bodyModal"> </slot>
        <template #modal-footer="{ salvar, cancelar }">
          <!-- Emulate built in modal footer ok and cancel button actions -->
        <slot name="footerModal"></slot>
        </template>
      </b-modal>
    </div>

    <div v-else-if="typeModal === 'adicionarModal'">
      <b-modal @hidden="cancelarFunc('adicionarInformacao')" id="modal-adicionarInformacao" :title="'Adicionar informação:'">
        <template #modal-header>
          <slot name="headerModal">
          </slot>
        </template>
        <slot name="bodyModal"> </slot>
        <template #modal-footer="{ salvar, cancelar }">
          <!-- Emulate built in modal footer ok and cancel button actions -->
        <slot name="footerModal"></slot>
        </template>
      </b-modal>
    </div>

    <div v-else-if="typeModal === 'limparModal'">
      <b-modal @hidden="cancelarFunc('limparInformacao')" id="modal-limparInformacoes"
   >
        <template #modal-header>
          <slot name="headerModal">
          </slot>
        </template>
        <slot name="bodyModal"> </slot>
        <template #modal-footer="{ salvar, cancelar }">
          <!-- Emulate built in modal footer ok and cancel button actions -->
        <slot name="footerModal"></slot>
        </template>
      </b-modal>
    </div>
  </div>

  
</template>

<script lang="ts">
import { Informacao } from "@/model/Model.Informacao.vue";

export default {
  name: "Modal",
  props: {
    typeModal: String,
  },

  created() {
  },
  data() {
    return {
      modalGerarInformacoes: false as boolean,
      modalCustom: false as boolean,
      modalEditarInformacao: false as boolean,

      name: null as number | null,
      labelStr: '',
      nameState: false as boolean | null,
    };
  },
  methods: {
    stateInputFunc(refModal: string) {
      switch (refModal) {
        case 'gerarInformacoes':
          gerarInformacoes(this);
          break;
      }

      function gerarInformacoes(thisParent: any) {
        const label = document.querySelector("#labelInput"),
          btnSucess = document.querySelector("#successBtn");

        if ((thisParent as any).name === undefined || (thisParent as any).name === null || (thisParent as any).name == 0) {
          label?.setAttribute(
            "invalid-feedback",
            "Não é possivel gerar informações com o valor nulo"
          );
          btnSucess?.setAttribute("disabled", "true");
          (thisParent as any).nameState = false;
          return;
        } else {
          if ((thisParent as any).name.match(/[^0-9\.]/g)) {
            (thisParent as any).name = (thisParent as any).name.replace(/[^0-9\.]/g, '');
            thisParent.labelStr = "Digite apenas numeros.";
            (thisParent as any).nameState = false;

          } else {
            btnSucess?.removeAttribute("disabled");
            (thisParent as any).nameState = true;
            return;
          }
        }
        (thisParent as any).nameState = null;
        return;
      }

    },
    cancelarFunc(modal: string) {
      switch (modal) {
        case 'gerarInformacoes':
          this.$data.name = null;
          this.$data.nameState = null;
          (this as any).modalGerarInformacoes = false;
          break;
        case 'editarInformacao':
          (this as any).modalEditarInformacao = false;
      }

      return;
    },
    salvarInformacaoGerada(bvModalEvent: Event) {
      bvModalEvent.preventDefault();
      (this as any).handleSubmit('gerarInformacoes');
    },

    handleSubmit(tipoModal: string) {
      switch (tipoModal) {
        case 'gerarInformacoes':
          let listInformacoes: Informacao[];
          if ((this as any).name !== null)
            listInformacoes = Informacao.gerarInformacoes(Number((this as any).name));
          else throw Error("Não é possivel gerar valor nulo por aqui");

          if (this.$root.$refs.Render !== undefined)
            (this as any).$root.$refs.Render.add(listInformacoes);
          (this as any).cancelarFunc(tipoModal);

          break;
      }


    }

  }
};
</script>

<style lang="scss" scoped>

</style>
