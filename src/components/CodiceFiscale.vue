<template>
  <div>
    <h1 class="mb-5">Generatore codice fiscale</h1>
    <div class="container">
      <div class="row justify-content-center">
        <div class="col-12 col-lg-6 text-center">
          <form id="" role="form" @submit.prevent="submitFiscalCode()" autocomplete="on">
            <div class="form-floating mb-3">
              <input type="text" class="form-control" v-model="name" id="name" placeholder="Nome" required>
              <label for="name">Nome</label>
            </div>
            <div class="form-floating mb-3">
              <input type="text" class="form-control" v-model="surname" id="surname" placeholder="Cognome" required>
              <label for="surname">Cognome</label>
            </div>
            <div class="form-check form-check-inline mb-3">
              <input class="form-check-input" type="radio" name="inlineRadioOptions" v-model="sex" id="male" value="M" required>
              <label class="form-check-label" for="male">M</label>
            </div>
            <div class="form-check form-check-inline mb-3">
              <input class="form-check-input" type="radio" name="inlineRadioOptions" v-model="sex" id="female" value="F" required>
              <label class="form-check-label" for="female">F</label>
            </div>
            <div class="mb-3">Luogo di Nascita</div>
            <select class="form-select form-select-lg mb-3" v-model="region" aria-label=".form-select-lg example" required>
              <option selected>Luogo di Nascita (Regione)</option>
              <option v-for="region in regions" :key="region" :value="region">{{region}}</option>
            </select> 
            <select class="form-select form-select-lg mb-3" v-if="region" v-model="birthPlace" aria-label=".form-select-lg example" required>
              <option selected>Luogo di Nascita (comune)</option>
              <option v-for="nameCode in nameCodes" :key="nameCode[1]" :value="nameCode[1]">{{nameCode[0]}}</option>
            </select>     
            <div class="mb-3">
              <label for="birthday" class="form-label">Giorno di nascita</label>
              <input type="date" class="form-control" v-model="birthday" id="birthday" required>
            </div>
            <div class="form-group">
                <button role="button" type="submit" class="btn btn-primary lead py-2 px-3">Genera codice fiscale</button>
            </div>
          </form>
        </div>
        <div class="row mt-5">
          <div class="col-12">
            <h1>{{fiscalCode}}</h1>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'CodiceFiscale',
  data () {
    return {
      name: '',
      surname: '',
      sex: '',
      birthday: '',
      birthPlace: '',
      commonRegion: '',
      regions: '',
      fiscalCode: '',
      region: '',
      nameCodes: '',
    }
  },
  mounted() {
    fetch('https://raw.githubusercontent.com/matteocontrini/comuni-json/master/comuni.json')
      .then(result => result.json())
      .then(dati => {
          this.commonRegion = dati.map(el=>[el.nome, el.codiceCatastale, el.regione.nome]);
          this.regions = new Set(dati.map(el=>el.regione.nome).sort())
          console.log(this.regions);
      })
  },
  methods: {
    submitFiscalCode(){
      let result = '';
      console.log(this.name, this.surname, this.sex, this.birthday, this.birthPlace);
      let s = this.surname.replace(/ /g, "").toUpperCase().split('')
      let n = this.name.replace(/ /g, "").toUpperCase().split('')
      let consonant = [];
      let vocal = [];
      s.forEach(el => {
        if(el == 'A' || el == 'E' || el == 'I' || el == 'O' || el == 'U'){
          vocal.push(el)
        } else {
          consonant.push(el)
        }
      })
      if(consonant.length >= 3){
        result = consonant.join('').substring(0, 3)
      } else if(consonant.length == 2) {
        result = consonant.join('') + vocal[0]
      } else if(consonant.length == 1) {
        if (vocal.length >= 2) {
          result = consonant.join('') + vocal[0] + vocal[1]
        } else {
          result = consonant.join('') + vocal[0] + 'X'
        }
      } else if (vocal.length >= 3) {
        result = vocal.join('').substring(0, 3)
      } else {
        if (vocal.length == 2) {
          result = vocal.join('') + 'X'
        } else {
          result = vocal.join('') + 'XX' 
        }
      }
      consonant = [];
      vocal = [];
      n.forEach(el => {
        if(el == 'A' || el == 'E' || el == 'I' || el == 'O' || el == 'U'){
          vocal.push(el)
        } else {
          consonant.push(el)
        }
      })
      if(consonant.length >= 4){
        result += consonant[0] + consonant[consonant.length - 2] + consonant[consonant.length - 1] 
      } else if(consonant.length == 3) {
        result += consonant.join('')
      } else if(consonant.length == 2) {
        result += consonant.join('') + vocal[0]
      } else if(consonant.length == 1) {
        if (vocal.length >= 2) {
          result += consonant.join('') + vocal[0] + vocal[1]
        } else {
          result += consonant.join('') + vocal[0] + 'X'
        }
      } else if (vocal.length >= 3) {
        result += vocal.join('').substring(0, 3)
      } else {
        if (vocal.length == 2) {
          result += vocal.join('') + 'X'
        } else {
          result += vocal.join('') + 'XX' 
        }
      }
      let b = this.birthday.split('-')
      result += b[0].substring(2, 4)
      switch (b[1]) {
        case '01':
          result += 'A'
          break;
        case '02':
          result += 'B'
          break;
        case '03':
          result += 'C'
          break;
        case '04':
          result += 'D'
          break;
        case '05':
          result += 'E'
          break;
        case '06':
          result += 'H'
          break;
        case '07':
          result += 'L'
          break;
        case '08':
          result += 'M'
          break;
        case '09':
          result += 'P'
          break;
        case '10':
          result += 'R'
          break;
        case '11':
          result += 'S'
          break;
        case '12':
          result += 'T'
          break;
      }
      if(this.sex == 'F'){
        result += (Number(b[2]) + 40) 
      } else {
        result += b[2]
      }
      result += this.birthPlace
    let controllCaracter = 0;
    let arr = result.split('');
    arr.forEach((el, index) => {
      if(index % 2 == 0){
        switch (el) {
          case 'A':
            controllCaracter += 1
            break;
          case '0':
            controllCaracter += 1
            break;
          case 'B':
            controllCaracter += 0
            break;
          case '1':
            controllCaracter += 0
            break;
          case 'C':
            controllCaracter += 5
            break;
          case '2':
            controllCaracter += 5
            break;
          case 'D':
            controllCaracter += 7
            break;
          case '3':
            controllCaracter += 7
            break;
          case 'E':
            controllCaracter += 9
            break;
          case '4':
            controllCaracter += 9
            break;
          case 'F':
            controllCaracter += 13
            break;
          case '5':
            controllCaracter += 13
            break;
          case 'G':
            controllCaracter += 15
            break;
          case '6':
            controllCaracter += 15
            break;
          case 'H':
            controllCaracter += 17
            break;
          case '7':
            controllCaracter += 17
            break;
          case 'I':
            controllCaracter += 19
            break;
          case '8':
            controllCaracter += 19
            break;
          case 'J':
            controllCaracter += 21
            break;
          case '9':
            controllCaracter += 21
            break;
          case 'K':
            controllCaracter += 2
            break;
          case 'L':
            controllCaracter += 4
            break;
          case 'M':
            controllCaracter += 18
            break;
          case 'N':
            controllCaracter += 20
            break;
          case 'O':
            controllCaracter += 11
            break;
          case 'P':
            controllCaracter += 3
            break;
          case 'Q':
            controllCaracter += 6
            break;
          case 'R':
            controllCaracter += 8
            break;
          case 'S':
            controllCaracter += 12
            break;
          case 'T':
            controllCaracter += 14
            break;
          case 'U':
            controllCaracter += 16
            break;
          case 'V':
            controllCaracter += 10
            break;
          case 'W':
            controllCaracter += 22
            break;
          case 'X':
            controllCaracter += 25
            break;
          case 'Y':
            controllCaracter += 24
            break;
          case 'Z':
            controllCaracter += 23
            break; 
        }
      } else {
        switch (el) {
          case 'A':
            controllCaracter += 0
            break;
          case '0':
            controllCaracter += 0
            break;
          case 'B':
            controllCaracter += 1
            break;
          case '1':
            controllCaracter += 1
            break;
          case 'C':
            controllCaracter += 2
            break;
          case '2':
            controllCaracter += 2
            break;
          case 'D':
            controllCaracter += 3
            break;
          case '3':
            controllCaracter += 3
            break;
          case 'E':
            controllCaracter += 4
            break;
          case '4':
            controllCaracter += 4
            break;
          case 'F':
            controllCaracter += 5
            break;
          case '5':
            controllCaracter += 5
            break;
          case 'G':
            controllCaracter += 6
            break;
          case '6':
            controllCaracter += 6
            break;
          case 'H':
            controllCaracter += 7
            break;
          case '7':
            controllCaracter += 7
            break;
          case 'I':
            controllCaracter += 8
            break;
          case '8':
            controllCaracter += 8
            break;
          case 'J':
            controllCaracter += 9
            break;
          case '9':
            controllCaracter += 9
            break;
          case 'K':
            controllCaracter += 10
            break;
          case 'L':
            controllCaracter += 11
            break;
          case 'M':
            controllCaracter += 12
            break;
          case 'N':
            controllCaracter += 13
            break;
          case 'O':
            controllCaracter += 14
            break;
          case 'P':
            controllCaracter += 15
            break;
          case 'Q':
            controllCaracter += 16
            break;
          case 'R':
            controllCaracter += 17
            break;
          case 'S':
            controllCaracter += 18
            break;
          case 'T':
            controllCaracter += 19
            break;
          case 'U':
            controllCaracter += 20
            break;
          case 'V':
            controllCaracter += 21
            break;
          case 'W':
            controllCaracter += 22
            break;
          case 'X':
            controllCaracter += 23
            break;
          case 'Y':
            controllCaracter += 24
            break;
          case 'Z':
            controllCaracter += 25
            break;
        }
      }
    })
    let numberToLetter = controllCaracter % 26
    let alphabet = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z']
    result += alphabet[numberToLetter]
    this.fiscalCode = result
    }
  },
  watch: {
    region(r){
      this.nameCodes = this.commonRegion.filter(el => el[2] == r)
      console.log(this.nameCodes);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
