  
<script>
import axios from 'axios';
export default {
    data() {
        return {
            nome: '',
            cognome: '',
            giorno: '',
            mese: '',
            anno: '',
            genere: [],
            luogoNascita: '',
            codiceFiscale: '',
            nomeCitta: '',
            comuni: [],
            codiceCatastale: '',
            carattereControllo: '',
        };
    },
    computed: {
        giorni() {
            return Array.from({ length: 31 }, (_, i) => (i + 1).toString().padStart(2, '0'));
        },
        mesi() {
            return [
                'Gennaio', 'Febbraio', 'Marzo', 'Aprile',
                'Maggio', 'Giugno', 'Luglio', 'Agosto',
                'Settembre', 'Ottobre', 'Novembre', 'Dicembre'
            ];
        },
        anni() {
            const currentYear = new Date().getFullYear();
            return Array.from({ length: 100 }, (_, i) => currentYear - i);
        }
    },
    methods: {

        ottieniConsonanti(parola) {
            return parola.replace(/[aeiouAEIOU]/g, ''); // Rimuovo le vocali
        },
        ottieniVocali(parola) {
            return (parola.match(/[aeiouAEIOU]/g) || []).join('');
        },



        calcolaCarattereControllo(codiceFiscaleBase) {
            const caratteriDispari = {
                '0': 1, '1': 0, '2': 5, '3': 7, '4': 9, '5': 13, '6': 15, '7': 17, '8': 19, '9': 21,
                'A': 1, 'B': 0, 'C': 5, 'D': 7, 'E': 9, 'F': 13, 'G': 15, 'H': 17, 'I': 19, 'J': 21,
                'K': 2, 'L': 4, 'M': 18, 'N': 20, 'O': 11, 'P': 3, 'Q': 6, 'R': 8, 'S': 12, 'T': 14,
                'U': 16, 'V': 10, 'W': 22, 'X': 25, 'Y': 24, 'Z': 23
            };

            const caratteriPari = {
                '0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9,
                'A': 0, 'B': 1, 'C': 2, 'D': 3, 'E': 4, 'F': 5, 'G': 6, 'H': 7, 'I': 8, 'J': 9,
                'K': 10, 'L': 11, 'M': 12, 'N': 13, 'O': 14, 'P': 15, 'Q': 16, 'R': 17, 'S': 18, 'T': 19,
                'U': 20, 'V': 21, 'W': 22, 'X': 23, 'Y': 24, 'Z': 25
            };

            let sommaDispari = 0;
            let sommaPari = 0;

            for (let i = 0; i < codiceFiscaleBase.length; i++) {
                const carattere = codiceFiscaleBase.charAt(i);
                if (i % 2 === 0) {  // Cambiato da !== a ===
                    sommaDispari += caratteriDispari[carattere];
                } else {
                    sommaPari += caratteriPari[carattere];
                }
            }

            const sommaTotale = (sommaDispari + sommaPari) % 26;

            const caratteriLista = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z'];
            const letteraControllo = caratteriLista[sommaTotale];

            return letteraControllo;
        }


        ,


        generaCodiceFiscale() {
            let nomeCodice = '';
            let cognomeCodice = '';
            let annoCodice = '';
            let giornoCodice = '';

            // Logica per generare il codice fiscale basato su nome e cognome


            let cognomeConsonanti = this.ottieniConsonanti(this.cognome).slice(0, 3).toUpperCase();
            let nomeConsonanti = this.ottieniConsonanti(this.nome).slice(0, 3).toUpperCase();
            let cognomeVocali = this.ottieniVocali(this.cognome)
            let nomeVocali = this.ottieniVocali(this.nome)
            //se è minore di 3 caratteri il nome allora deve aggiungere una vocale più una x
            if (this.nome.length < 3) {
                nomeCodice = (nomeConsonanti + nomeVocali[0]).toString() + 'X';
                //nel caso in cui le consonanti del nome sono minori di 3 va aggiunta una vocale
            } else if (nomeConsonanti.length < 3) {
                nomeCodice = (nomeConsonanti + nomeVocali).toString();
                //nel caso in cui il nome abbia 4 o più consonanti bisogna prendere gli elementi pari
            } else if (nomeConsonanti.length >= 4) {
                nomeCodice = nomeConsonanti[0] + nomeConsonanti[2] + nomeConsonanti[4];

            } else {
                nomeCodice = nomeConsonanti;
            }
            if (this.cognome.length < 3) {
                cognomeCodice = (cognomeConsonanti + cognomeVocali[0]).toString() + 'X';
            } else if (nomeConsonanti.length < 3) {
                cognomeCodice = (cognomeConsonanti + cognomeVocali).toString();
            }

            else {
                cognomeCodice = cognomeConsonanti;
            }

            //anno di nascita
            annoCodice = this.anno.toString().slice(-2);
            console.log(nomeCodice)
            console.log(nomeVocali[0])

            //mese
            // Definisco un array di lettere
            const lettereMesi = ['A', 'B', 'C', 'D', 'E', 'H', 'L', 'M', 'P', 'R', 'S', 'T'];

            let indiceMese = this.mese - 1; // Sottraggo 1 per l'array

            // Associa la lettera del mese selezionato
            let letteraMese = lettereMesi[indiceMese];


            console.log(letteraMese);

            //giorno nascita e sesso

            console.log(this.genere)

            if (this.genere.includes('M')) {
                giornoCodice = parseInt(this.giorno);
            } else if (this.genere.includes('F')) {
                giornoCodice = parseInt(this.giorno) + 40;
            }
            console.log(giornoCodice)


            // Calcola il carattere di controllo
            let codiceFiscaleBase = cognomeCodice.slice(0, 3).toUpperCase() + nomeCodice.slice(0, 3).toUpperCase() + annoCodice + letteraMese + giornoCodice + this.codiceCatastale;
            let carattereControllo = this.calcolaCarattereControllo(codiceFiscaleBase);

            // Aggiungo il carattere di controllo al codice fiscale base
            let codiceFiscaleGenerato = codiceFiscaleBase + carattereControllo;
            this.codiceFiscale = codiceFiscaleGenerato;
        },

        async cercaComune() {
            try {
                const response = await axios.get(`https://axqvoqvbfjpaamphztgd.functions.supabase.co/comuni?nome=${this.nomeCitta}`);
                this.comuni = response.data;
                this.codiceCatastale = response.data[0]?.codiceCatastale || 'nessun comune trovato';
                console.log(this.codiceCatastale)



            } catch (error) {
                console.error('Errore durante la ricerca del comune:', error);
            }
        },
        selezionaComune(comune) {
            this.nomeCitta = comune.nome;
            this.codiceCatastale = comune.codiceCatastale;
            console.log(this.codiceCatastale);
            this.comuni = [];

        },

        pulisciForm() {
            this.nome = '';
            this.cognome = '';
            this.giorno = '';
            this.mese = '';
            this.anno = '';
            this.genere = [];
            this.luogoNascita = '';
            this.codiceFiscale = '';
            this.nomeCitta = '';
            this.comuni = [];
            this.codiceCatastale = '';
            this.carattereControllo = '';

        }
    },

}

</script>
  
<template>
    <div class="card-container">

        <form @submit.prevent="generaCodiceFiscale">
            <label for="nome">Nome:</label>
            <input v-model="nome" type="text" id="nome" name="nome" required>

            <label for="cognome">Cognome:</label>
            <input v-model="cognome" type="text" id="cognome" name="cognome" required>

            <label for="dataNascita">Data di Nascita:</label>
            <div class="select-container">
                <select v-model="giorno" required>
                    <option value="" disabled>Giorno</option>
                    <option v-for="g in giorni" :key="g" :value="g">{{ g }}</option>
                </select>
                <select v-model="mese" required>
                    <option value="" disabled>Mese</option>
                    <option v-for="(mese, index) in mesi" :key="index + 1" :value="index + 1">{{ mese }}</option>
                </select>
                <select v-model="anno" required>
                    <option value="" disabled>Anno</option>
                    <option v-for="a in anni" :key="a" :value="a">{{ a }}</option>
                </select>
            </div>
            <label for="genere">Genere:</label>
            <div class="d-flex ">
                <div class="form-check form-switch">
                    <input class="form-check-input" type="checkbox" id="flexSwitchCheckDefault" v-model="genere" value="M">
                    <label class="form-check-label" for="flexSwitchCheckDefault">Uomo</label>
                    <input class="form-check-input" type="checkbox" id="flexSwitchCheckDefault" v-model="genere" value="F">
                    <label class="form-check-label" for="flexSwitchCheckDefault">Donna</label>
                </div>
                <div v-if="codiceFiscale" class="card risultato">
                    <h3>Codice Fiscale Generato:</h3>
                    <h4 class="cod">{{ codiceFiscale }}</h4>
                </div>
            </div>
            <div>
                <label for="nomeCitta">Città Di Nascita:</label>
                <input v-model="nomeCitta" type="text" id="nomeCitta" name="nomeCitta" @input="cercaComune">

                <div v-if="comuni.length > 0" class="tendina rounded-3 bg-dark text-light">
                    <ul>
                        <li class="" v-if="nomeCitta" v-for="comune in comuni" :key="comune.nome"
                            @click="selezionaComune(comune)">
                            {{ comune.nome }}
                        </li>
                    </ul>
                </div>
            </div>
            <button class="btn btn-dark me-3" type="submit">Genera Codice Fiscale</button>
            <button class="btn btn-dark" type="submit" @click="pulisciForm">Genera Un Nuovo Codice Fiscale</button>


        </form>


    </div>
</template>

<style lang="scss">
.card-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    background-color: #378aa3;

    border-radius: 10px;

    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);

    margin: 20px;
    padding: 20px;
    color: white;
}

.card {
    background-color: #378aa3;
    color: white;

    border-radius: 10px;

    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);

    margin-left: 100px;
    padding: 10px;
}

.cod {
    background-color: #378aa3;
    color: white;

    border-radius: 10px;

    box-shadow: 10px 10px 10px 10px rgba(0, 0, 0, 0.329);

    margin-left: 100px;
    padding: 10px;
}

.risultato {}

.tendina {
    position: absolute;
    border: 1px solid #ccc;
    max-height: 150px;
    overflow-y: auto;
    background-color: white;
}

.tendina ul {
    list-style: none;
    padding: 0;
    margin: 0;
}

.tendina li {
    padding: 8px;
    cursor: pointer;
}

.tendina li:hover {
    background-color: #393939;
}

body {
    font-family: Arial, sans-serif;
    background: white;
}

form {
    width: 600px;
}

label {
    display: block;
    margin-bottom: 8px;
}

input,
select {
    width: 100%;
    padding: 8px;
    margin-bottom: 16px;
    box-sizing: border-box;
    border-radius: 30px;
    box-shadow: 10px 10px rgba(49, 48, 48, 0.294);
}

.select-container {
    display: flex;
    border-radius: 30px;
}

button {
    background-color: #4CAF50;
    color: white;
    padding: 10px 15px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
}

button:hover {
    background-color: #45a049;
}

@media screen and (max-width: 600px) {
    form {
        max-width: 100%;
    }
}

@media screen and (max-width: 768px) {
    .select-container {
        flex-direction: column;
    }

    select {
        margin-bottom: 8px;
    }
}
</style>

  