<template>
  <div id="principal">
    <div id= "calculadora">
      <div class="pantalla" :class="{pantallaHistory: showHistory}">
        <v-row v-if="!showHistory && inputName">
          <v-col cols="9">
            <v-text-field
              v-on:keyup.enter="saveName"
              v-model="name"
              label="Ingresa tu nombre"
              hide-details
              solo
              dense
            ></v-text-field>
          </v-col>
          <v-col cols="3">
            <v-btn @click="saveName">
              <v-icon>
                mdi-arrow-right-circle-outline
              </v-icon>
            </v-btn>
          </v-col>
        </v-row>
        <div v-else>
          <h4 class="welcome" v-if="!showHistory">
            Bienvenido {{ name }}
            <v-btn
              @click="clearName"
              icon
            >
              <v-icon class="color-danger">mdi-close-circle-outline</v-icon>
            </v-btn>
          </h4>
        </div>
        <v-row v-if="!inputName && !showHistory">
          <v-col cols="3">
            <v-text-field
              v-model="operands1"
              @input="validarInput"
              @click="inputClicked(1)"
              class="operands"
              ref="operands1"
              hide-details
              solo
              dense
            ></v-text-field>
          </v-col>
          <v-col class="p-0" cols="1">
            <input
              v-model="operator1"
              class="operator"
              maxlength="1"
            />
          </v-col>
          <v-col cols="3">
            <v-text-field
              v-model="operands2"
              @input="validarInput"
              @click="inputClicked(2)"
              class="operands"
              ref="operands2"
              hide-details
              solo
              dense
            ></v-text-field>
          </v-col>
          <v-col class="p-0" cols="1">
            <input
              v-model="operator2"
              class="operator"
              maxlength="1"
            />
          </v-col>
          <v-col cols="4">
            <v-text-field
              v-model="operands3"
              @input="validarInput"
              @click="inputClicked(3)"
              class="operands"
              hide-details
              dense
              solo
            ></v-text-field>
          </v-col>
        </v-row>
        <v-container v-if="!showHistory" class="container-result" fluid>
          <v-row>
            <v-col v-if="!inputName && showResult" class="p-0" cols="11">
              <h3>= {{ result }}</h3>
              <h5 v-if="madeBy">{{ madeBy }}</h5>
            </v-col>
            <v-col v-if="!inputName && !result && required" class="p-0" cols="11">
              <small class="color-danger">Este campo es requerido.</small>
            </v-col>
          </v-row>
        </v-container>
        <div v-else>
          <v-btn
            class="btn-back"
            @click="showHistory = false"
            small
          >
            Volver
          </v-btn>
          <br>
          <div v-if="history">
            <h3>Historial de operaciones</h3>
            <div class="flex flex-column" v-for="(item, index) in history" :key="index">
              <div>{{ index+1 }}) Historial de {{ item.user }}</div>
              <div v-for="(operation, indexOp) in item.operations" :key="indexOp">
                  {{ operation.expression }} = {{ operation.result }}
              </div>
              <br>
            </div>
          </div>
          <h3 v-else>No hay historial de operaciones </h3>
        </div>
      </div>
      <div class="teclado buttons" :class="{ hideTeclado: showHistory }">
        <button class="button" @click="getHistory">
          <v-icon class="history">mdi-history</v-icon>
        </button>
        <button class="button" @click="clearAll">
          <v-icon class="clear">mdi-trash-can-outline</v-icon>
        </button>
        <button class="button">%</button>
        <button class="button">/</button><br>
        <button class="button" @click="numberSelected(7)">7</button>
        <button class="button" @click="numberSelected(8)">8</button>
        <button class="button" @click="numberSelected(9)">9</button>
        <button class="button">*</button><br>
        <button class="button" @click="numberSelected(4)">4</button>
        <button class="button" @click="numberSelected(5)">5</button>
        <button class="button" @click="numberSelected(6)">6</button>
        <button class="button">-</button><br>
        <button class="button" @click="numberSelected(1)">1</button>
        <button class="button" @click="numberSelected(2)">2</button>
        <button class="button" @click="numberSelected(3)">3</button>
        <button class="button">+</button><br>
        <button class="button" @click="numberSelected('.')">.</button>
        <button class="button" @click="numberSelected(0)">0</button>
        <button class="button result" @click="operation">=</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'CalculadoraComponent',
  data() {
    return {
      name: '',
      inputName: true,

      operands1: '',
      operands2: '',
      operands3: '',
      operator1: '+',
      operator2: '+',

      required: false,
      errorInput1: false,
      errorInput2: false,

      showResult: false,
      result: '',

      history: [],
      showHistory: false,
      historyExist: null,
      madeBy: '',

      clickedInput: null
    }
  },
  mounted() {
    window.addEventListener('beforeunload', localStorage.clear())
  },
  beforeDestroy() {
    window.removeEventListener('beforeunload', localStorage.clear())
  },
  methods: {
    clearName(){
      this.inputName = true
      this.showResult = false
      this.name = ''
      this.result = ''
      this.operands1 = ''
      this.operands2 = ''
      this.operands3 = ''
    },
    saveName () {
      if (this.name != '') {
        this.inputName = false
      }
    },
    inputClicked(inputNumber) {
      this.clickedInput = inputNumber
    },
    numberSelected(selected) {
      if (this.clickedInput == 1) {
        this.operands1 += selected
      } else if (this.clickedInput == 2) {
        this.operands2 += selected
      } else if (this.clickedInput == 3) {
        this.operands3 += selected
      }
    },
    operation () {
      if (this.operands1 == '') {
        this.required = true
        this.$nextTick(() => {
          this.$refs['operands1'].$el.querySelector('#input-7').focus();
        });

        return false
      }

      if (this.operands2 == '') {
        this.required = true
        this.$nextTick(() => {
          this.$refs['operands2'].$el.querySelector('#input-8').focus();
        });

        return false
      }

      if (isNaN(this.operands1) || isNaN(this.operands2) || isNaN(this.operands3)) {
        this.showResult = true
        this.result = this.operands1 + this.operands2 + this.operands3
      } else {
        let expression = this.operands1 + this.operator1 + this.operands2

        if (this.operands3 !== '') {
          expression += this.operator2 + this.operands3
        }

        this.result = eval(expression)
        this.showResult = true

        let history = [{
          user: this.name,
          operations: [{
            expression: expression,
            result: this.result
          }]
        }]

        this.historyExist =  JSON.parse(localStorage.getItem('history'))
        this.historyExist ? this.updateHistory(history) : this.saveHistory(history)
      }
    },
    saveHistory(history) {
      localStorage.setItem('history', JSON.stringify(history))
    },
    updateHistory(history) {
      this.findOperation(...history)
      this.historyExist.push(...history)
      localStorage.setItem('history', JSON.stringify(this.historyExist))
    },
    findOperation(nuevaOp) {
      let operaciones = this.historyExist

      for (let i = 0; i < operaciones.length; i++) {
        const user = operaciones[i].user
        const operacionesUsuario = operaciones[i].operations

        for (let j = 0; j < operacionesUsuario.length; j++) {
          const expresion = operacionesUsuario[j].expression

          if (expresion === nuevaOp.operations[0].expression) {
            this.madeBy = `Operación realizada por ${user}`
            if (user === nuevaOp.user) {
              this.madeBy = 'Ya realizaste esta operación antes'
            }
          }
        }
      }
    },
    getHistory() {
      this.showHistory = true
      this.history = JSON.parse(localStorage.getItem('history'))
    },
    clearAll() {
      this.operands1 = ''
      this.operands2 = ''
      this.operands3 = ''
      this.showResult = false
    },
    validarInput(e) {
      var patron = /^[a-zA-Z0-9]*$/; // Expresión regular para permitir solo caracteres alfanuméricos

      if (!patron.test(e)) {
       //error
      }
    }
  }
}
</script>

<style>
.p-0 {
  padding: 1px!important;
}

.welcome {
  margin-bottom: 10px!important;
}

#principal{
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

#calculadora {
  box-shadow: 0 4px 4px 0 rgba(0, 0, 0, 0.5), 0 6px 10px 0 rgba(0, 0, 0, 0.5);
  -moz-box-shadow:0 4px 4px 0 rgba(0, 0, 0, 0.5), 0 6px 10px 0 rgba(0, 0, 0, 0.5);
  -webkit-box-shadow: 0 4px 4px 0 rgba(0, 0, 0, 0.5), 0 6px 10px 0 rgba(0, 0, 0, 0.5);
}

.pantalla{
  background: white;
  background:#2cb5e8;
  font-size: 16px;
  height: 154px;
  border:0;
  margin:0;
  padding:10px;
  color: white;
  border-top-left-radius: 2px;
  border-top-right-radius: 2px;
  transition: height .3s;
}

.clearName {
  color: #D32F2F!important;
}

.pantallaHistory {
  height: 440px;
  width: 288px;
}

.container-result {
  margin-top: 10px!important;
}
.teclado{
  background: #3A434F;
}

.hideTeclado{
  display: none;
}
.v-btn {
  min-width: 39px!important;
}
.v-list-item, .v-list-item__subtitle {
  color: #fff!important;
  font-size: 20px!important;

}

.v-list-item__subtitle {
  margin-left: 10px!important;
}

.clear {
  color: rgb(255, 255, 255)!important;
}

.history {
  color: #FFA726!important;
}

.operator {
  width: 18px;
  margin: 15px 0px;
  font-size: 20px;
}

.operands {
  width: 60px;
}

.button{
  background: #3A434F;
  font-size: 20px;
  margin: 0;
  padding: 15px;
  border: 0;
  color: #FCFCFD;
  width: 72px;
  height: 62px;
  min-width: 10px;
  min-height: 10px;
  transition: all .2s ease-in-out;
  -webkit-transition: all .2s ease-in-out;
  -moz-transition: all .2s ease-in-out;
}
.button.result{
  width: 144px ;
}
.button:hover{
  transform: scale(1.1);
  -webkit-transform: scale(1.1);
  background: #5A656F;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.2);
  -moz-box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.2);
  -webkit-box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.2);
}
.button:active{
  transform: scale(1);
}

.v-text-field.v-text-field--enclosed:not(.v-text-field--rounded)>.v-input__control>.v-input__slot {
  padding: 0 10px!important;
}
</style>
