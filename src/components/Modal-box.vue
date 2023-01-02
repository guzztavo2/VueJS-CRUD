<template>
  <div>
    <div v-if="typeModal === 'gerarInformacoes'">
      <b-modal
        id="modal-gerarInformacoes"
        v-model="modalGerarInformacoes"
        ref="modalGerarInformacoes"
        title="Digite em números a quantidade de informações que você quer gerar:"
      >
        <form ref="form" @submit.stop.prevent="handleSubmit">
          <b-form-group
            id="labelInput"
            label="Informação"
            label-for="informacao-input"
            invalid-feedback="Informar a quantidade das informações geradas é um requisito"
            valid-feedback="Tudo certo, só clicar em gerar para gerar os numeros"
            :state="nameState"
          >
            <b-form-input
              id="informacao-input"
              @keyup="stateInputFunc()"
              v-model="name"
              :state="nameState"
              required
            ></b-form-input>
          </b-form-group>
        </form>
        <template #modal-footer="{ salvar, cancelar }">
          <!-- Emulate built in modal footer ok and cancel button actions -->
          <b-button size="sm" variant="success" id="successBtn" @click="salvarFunc">
            Gerar
          </b-button>
          <b-button size="sm" variant="danger" @click="cancelarFunc"> Cancelar </b-button>
        </template>
      </b-modal>
    </div>
    <div v-else-if="typeModal === 'customModal'">
      <b-modal
        id="modal-Custom"
        ref="modalCustomInformacoes"
        title="titleModal"
      >
        <p>{{bodyModal}}</p>

        <template #modal-footer="{ cancelar }">
          <!-- Emulate built in modal footer ok and cancel button actions -->
          <b-button size="sm" variant="danger" @click="cancelarFunc"> Ok </b-button>
        </template>
      </b-modal>
    </div>
  </div>
</template>

<script lang="ts">
import { Informacao } from "@/model/Model.Informacao.vue";

export default {

  // computed: {
  //   nameState(event: Event): boolean|null {
  //     if (document.querySelector('labelInput') !== null) {
  //       const label = document.querySelector('labelInput');
  //       if (this.$data.name === 0 || this.$data.name === undefined) {
  //         console.log(label);
  //         alert('a');
  //         label?.setAttribute('invalid-feedback', 'Não é possivel gerar informações com o valor nulo');
  //         return false;
  //       }
  //       return true;
  //     }
  //     return null;
  //   }

  // },
  name: "Modal",
  props: {
    typeModal: '' as string,
    bodyModal:undefined as String|undefined,
    titleModal:undefined as String|undefined    
  },
  created() {},
  data() {
    return {
      modalGerarInformacoes: false,
      name: null as number | null,
      nameState: false as boolean | null,
    };
  },
  methods: {
    stateInputFunc() {
      const label = document.querySelector("#labelInput"),
        btnSucess = document.querySelector("#successBtn");
      // console.log(this.$data.name);
      if (this.$data.name == 0 || this.$data.name == Number("")) {
        label?.setAttribute(
          "invalid-feedback",
          "Não é possivel gerar informações com o valor nulo"
        );
        btnSucess?.setAttribute("disabled", "true");
        this.nameState = false;
        return;
      } else {
        btnSucess?.removeAttribute("disabled");
        this.nameState = true;
        return;
      }
      this.nameState = null;
      return;
    },
    cancelarFunc() {
      this.modalGerarInformacoes = false;
      return;
    },
    salvarFunc(bvModalEvent: any) {
      bvModalEvent.preventDefault();
      this.handleSubmit();
    },
    handleSubmit() {
      const render = this.$root.$refs.Render;
      let listInformacoes: Informacao[];
      if (this.$data.name !== null)
        listInformacoes = Informacao.gerarInformacoes(Number(this.name));
      else throw Error("Não é possivel gerar valor nulo por aqui");

      if (this.$root.$refs.Render !== undefined)
        (this as any).$root.$refs.Render.add(listInformacoes);

      this.cancelarFunc();
    },
  },
};
</script>

<style lang="scss" scoped></style>
