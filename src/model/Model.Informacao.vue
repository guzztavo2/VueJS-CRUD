

<script lang="ts">
export class Informacao {
  public id!: number;
  public informacao!: string;
  public dataCriacao!: Date;
  public dataAtualizacao!: Date;

  public constructor(informacao: string) {
    if (this.informacao === undefined) {
      this.informacao = informacao;
      this.dataCriacao = new Date();
    } else {
      this.informacao = informacao;
      this.dataAtualizacao = new Date();
    }
  }

  public static gerarInformacoes(tamanhoArray:number):Informacao[] {
    let data = ["Dickerson", "Larsen", "Geneva", "Jami"];
    let result:Informacao[] = [];
    for(let n = 1; n <= tamanhoArray; n++){
      result.push(new Informacao(data[Math.floor(Math.random() * data.length)]));
      result[n - 1].id = n;
    }
    console.log(result);
    return result;
  }
}
</script>

<style lang="scss" scoped></style>
