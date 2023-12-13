  
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
            return parola.replace(/[aeiouAEIOU]/g, ''); // Rimuovi le vocali
        },
        ottieniVocali(parola) {
            return (parola.match(/[aeiouAEIOU]/g) || []).join('');
        },

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
            // Definisci un array di lettere
            const lettereMesi = ['A', 'B', 'C', 'D', 'E', 'H', 'L', 'M', 'P', 'R', 'S', 'T'];

            // Supponiamo che il valore di this.mese sia l'indice del mese selezionato (1 per gennaio, 2 per febbraio, ecc.)
            let indiceMese = this.mese - 1; // Sottrai 1 perché gli array sono zero-based

            // Associa la lettera del mese selezionato
            let letteraMese = lettereMesi[indiceMese];

            // Ora puoi utilizzare letteraMese come desiderato
            console.log(letteraMese);

            //giorno nascita e sesso

            console.log(this.genere)

            if (this.genere.includes('M')) {
                giornoCodice = parseInt(this.giorno);
            } else if (this.genere.includes('F')) {
                giornoCodice = parseInt(this.giorno) + 40;
            }
            console.log(giornoCodice)
            let codiceFiscaleGenerato = cognomeCodice.slice(0, 3).toUpperCase() + nomeCodice.slice(0, 3).toUpperCase() + annoCodice + letteraMese + giornoCodice;

            this.codiceFiscale = codiceFiscaleGenerato;
        },

        async cercaComune() {
            try {
                const response = await axios.get(`https://axqvoqvbfjpaamphztgd.functions.supabase.co/comuni?nome=${this.nomeCitta}`);
                this.comuni = response.data;
            } catch (error) {
                console.error('Errore durante la ricerca del comune:', error);
            }
        },
        selezionaComune(comune) {
            this.nomeCitta = comune;
            this.comuni = [];
        },
    },

}

</script>
  
<template>
    <div>

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
            <label>
                <input type="checkbox" v-model="genere" value="M"> Maschile
            </label>
            <label>
                <input type="checkbox" v-model="genere" value="F"> Femminile
            </label>

            <div>
                <label for="nomeCitta">Nome Città:</label>
                <input v-model="nomeCitta" type="text" id="nomeCitta" name="nomeCitta" @input="cercaComune">

                <div v-if="comuni.length > 0" class="tendina">
                    <ul>
                        <li v-for="comune in comuni" :key="comune.nome" @click="selezionaComune(comune.nome)">
                            {{ comune.nome }}
                        </li>
                    </ul>
                </div>
            </div>
            <button type="submit">Genera Codice Fiscale</button>
        </form>

        <div v-if="codiceFiscale">
            <h3>Codice Fiscale Generato:</h3>
            <p>{{ codiceFiscale }}</p>
        </div>
    </div>
</template>

<style lang="scss">
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
    background-color: #eee;
}

body {
    font-family: Arial, sans-serif;
    margin: 20px;
}

form {
    max-width: 400px;
    margin: auto
}

label {
    display: block;
    margin-bottom: 8px
}

input,
select {
    width: 100%;
    padding: 8px;
    margin-bottom: 16px;
    box-sizing: border-box
}

.select-container {
    display: flex
}

button {
    background-color: #4CAF50;
    color: white;
    padding: 10px 15px;
    border: none;
    border-radius: 4px;
    cursor: pointer
}

button:hover {
    background-color: #45a049
}
</style>
  