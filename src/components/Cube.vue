<template>
  <b-row align-h="center">
    <b-col class="test" cols="10">
      <b-card title="Cube together" border-variant="primary" id="cube">
        <div>
          <!--Titre + bouton version-->
          <p v-if="language === 'french'">
            Calculateur de qualité de résolution
          </p>
          <p v-if="language === 'english'">Quality solve calculator</p>
          <p v-if="language === 'french'">
            Pensé par
            <a
              href="https://www.youtube.com/channel/UC04tRiZgDsyvGj3wcTNg7TA/featured"
              target="_blank"
              >DamarusRex</a
            >
            développé avec
            <a
              href="https://www.linkedin.com/in/charles-delannois-9416a6193/"
              target="_blank"
              >CDelannois</a
            >
          </p>
          <p v-if="language === 'english'">
            Designed by
            <a
              href="https://www.youtube.com/channel/UC04tRiZgDsyvGj3wcTNg7TA/featured"
              target="_blank"
              >DamarusRex</a
            >
            developped with
            <a
              href="https://www.linkedin.com/in/charles-delannois-9416a6193/"
              target="_blank"
              >CDelannois</a
            >
          </p>
          <b-button
            v-if="language === 'french'"
            size="sm"
            @click="switchToEnglish()"
          >
            English version
          </b-button>
          <b-button
            v-if="language === 'english'"
            size="sm"
            @click="switchToFrench()"
          >
            Version française
          </b-button>
        </div>

        <!--Champs de valeur + bouton calculer-->
        <div>
          <b-row align-h="center" class="mt-3">
            <b-col cols="5">
              <p v-if="language === 'french'">Moyenne</p>
              <p v-if="language === 'english'">Average</p>
            </b-col>
            <b-col cols="5" id="ecart_type">
              <p v-if="language === 'french'">Ecart-Type</p>
              <p v-if="language === 'english'">Std. Deviation</p>
              <b-tooltip
                v-if="language === 'french'"
                target="ecart_type"
                triggers="hover"
              >
                Message ici
              </b-tooltip>
              <b-tooltip
                v-if="language === 'english'"
                target="ecart_type"
                triggers="hover"
              >
                Message here
              </b-tooltip>
            </b-col>
          </b-row>

          <b-row align-h="center">
            <b-col cols="5"
              ><input class="input" type="text" v-model="average" />
            </b-col>
            <b-col cols="5"
              ><input class="input" type="text" v-model="deviation" />
            </b-col>
          </b-row>

          <b-button v-if="language === 'french'" @click="calculate()">
            Calculer
          </b-button>
          <b-button v-if="language === 'english'" @click="calculate()">
            Calculate
          </b-button>
        </div>

        <!--Messages d'erreur + paramètres -->
        <div>
          <div v-if="!valuesOK" class="mt-3">
            <p v-if="language === 'french'">
              Veuillez compléter les champs avec des valeurs en secondes.
            </p>
            <p v-if="language === 'english'">
              Please fill in the fields with values in seconds.
            </p>
            <div v-if="deviationIsBiggerThanAverage">
              <p v-if="language === 'french'">
                L'écart-type ne peut être plus grand que la moyenne.
              </p>
              <p v-if="language === 'english'">
                Deviation can't be greater than average.
              </p>
            </div>
          </div>

          <div v-else>
            <div class="mt-3">
              <p v-if="language === 'french'">Paramètres</p>
              <p v-if="language === 'english'">Settings</p>
            </div>
            <b-row>
              <b-col>
                <p v-if="language === 'french'">Succès Critique</p>
                <p v-if="language === 'english'">Critical Success</p>
                <p><span class="fas fa-less-than"></span> {{ success }}</p>
              </b-col>

              <b-col>
                <p v-if="language === 'french'">Succès</p>
                <p v-if="language === 'english'">Success</p>
                <p v-if="language === 'french'">
                  Entre {{ success }} et {{ failure }}
                </p>
                <p v-if="language === 'english'">
                  Between {{ success }} and {{ failure }}
                </p>
              </b-col>
            </b-row>

            <b-row>
              <b-col>
                <p v-if="language === 'french'">Échec</p>
                <p v-if="language === 'english'">Failure</p>
                <p v-if="language === 'french'">
                  Entre {{ failure }} et {{ criticalFailure }}
                </p>
                <p v-if="language === 'english'">
                  Between {{ failure }} and {{ criticalFailure }}
                </p>
              </b-col>

              <b-col>
                <p v-if="language === 'french'">Échec critique</p>
                <p v-if="language === 'english'">Critical Failure</p>
                <p>
                  <span class="fas fa-greater-than-equal"></span
                  >{{ criticalFailure }}
                </p>
              </b-col>
            </b-row>
          </div>
        </div>

        <!--Boutons + chronomètre-->
        <div>
          <div v-if="!timerOn">
            <b-button :disabled="disable" @keyup.space="start">Start</b-button>
          </div>

          <div v-else>
            <b-button @click="reset" class="m-3">Reset</b-button>
          </div>

          <div v-if="valuesOK" class="m-3">
            <div v-if="language === 'french'">
              <p>Appuyez sur la barre d'espace pour lancer le chronomètre.</p>
              <p>
                Appuyez sur n'importe quelle touche pour arrêter le chronomètre.
              </p>
            </div>

            <div v-if="language === 'english'">
              <p>Press space bar to start the stopwatch.</p>
              <p>Press any key to stop the stopwatch.</p>
            </div>
          </div>
          <div class="m-3">{{ formattedElapsedTime }}</div>
        </div>

        <!--Comment ça fonctionne?-->

        <p v-if="language === 'french'">Comment ça fonctionne?</p>
        <p v-if="language === 'english'">How it all works?</p>
      </b-card>
    </b-col>
  </b-row>
</template>

<script>
export default {
  name: "Cube",

  data() {
    return {
      language: "french",
      deviationIsBiggerThanAverage: false,
      average: 0,
      deviation: 0,
      valuesOK: false,
      success: "",
      failure: "",
      criticalFailure: "",
      timerOn: false,
      elapsedTime: 0,
      timer: undefined,
      disable: true,
    };
  },

  computed: {
    formattedElapsedTime() {
      const date = new Date(null);
      date.setSeconds(this.elapsedTime / 10);
      const utc = date.toUTCString();
      return utc.substr(utc.indexOf(":") - 2, 8);
    },
  },

  mounted() {
    window.addEventListener("keypress", () => {
      if (event.code === "Space") {
        if (!this.timerOn) {
          this.timerOn = true;
          this.timer = setInterval(() => {
            this.elapsedTime += 10;
          }, 10);
        }
      }
    });

    window.addEventListener("keypress", () => {
      if (this.timerOn) {
        clearInterval(this.timer);
      }
    });
  },

  methods: {
    switchToEnglish() {
      this.language = "english";
    },
    switchToFrench() {
      this.language = "french";
    },
    calculate() {
      let averageNumber;
      let deviationNumber;
      if (/^[0-9]/u.test(this.average) && /^[0-9]+$/u.test(this.deviation)) {
        averageNumber = parseInt(this.average);
        deviationNumber = parseInt(this.deviation);
        if (averageNumber > 0 && deviationNumber > 0)
          if (averageNumber > deviationNumber) {
            this.valuesOK = true;
            this.deviationIsBiggerThanAverage = false;
            this.success = Math.round(averageNumber - deviationNumber);
            this.failure = Math.round(averageNumber + deviationNumber);
            this.criticalFailure = Math.round(
              averageNumber + deviationNumber * 3
            );
            this.disable = false;
          } else {
            this.valuesOK = false;
            this.deviationIsBiggerThanAverage = true;
            this.disable = true;
          }
        else {
          this.valuesOK = false;
          this.disable = true;
        }
      } else {
        this.valuesOK = false;
        this.disable = true;
      }
    },
    start() {
      this.timerOn = true;
      this.timer = setInterval(() => {
        this.elapsedTime += 10;
      }, 10);
    },
    reset() {
      this.timerOn = false;
      this.elapsedTime = 0;
    },

    //https://codepen.io/akras14/pen/ModYBo
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.input {
  width: 40%;
}
</style>
