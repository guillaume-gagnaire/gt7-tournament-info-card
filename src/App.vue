<template>
  <div id="conf">
    <label>
      Circuit :
      <select v-model="conf.track">
        <optgroup
          v-for="(list, name) in tracksByName"
          :key="name"
          :label="name"
        >
          <option v-for="track in list" :key="track.id" :value="track.id">
            {{ track.name }}
          </option>
        </optgroup>
      </select>
    </label>
    <label>
      Voiture obligatoire :
      <select v-model="conf.car">
        <option :value="null">Pas de restriction</option>
        <option :value="false">Texte custom</option>
        <optgroup v-for="(list, name) in carsByBrand" :key="name" :label="name">
          <option v-for="car in list" :key="car.id" :value="car.id">
            {{ car.name }}
          </option>
        </optgroup>
      </select>
    </label>
    <label
      >Logo de l'organisateur : <FileUpload @done="teamLogo = $event"
    /></label>
    <label
      >Hauteur du logo :
      <input type="range" v-model="conf.teamLogoHeight" min="0" max="500" />
    </label>
    <button @click="exportToPng">Générer l'image</button>
  </div>
  <div id="main" ref="result">
    <header>
      <div class="padd">
        <input type="text" v-model="conf.title" class="title" />
        <div class="desc">
          <div class="icon">
            <img
              src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADAAAAAwCAYAAABXAvmHAAAAAXNSR0IArs4c6QAAAuxJREFUaEPt2d/rj3cYx/HH16/lR340rBiZmRhlDhwscqLMkWz+AENRziwlJYUlTrY4WaS25GRHI0VJpMmBEwczYiM/a2UtyoiQrrq/9f1+fe77/tw/Pp/vRz7v0/u6Xvf7eV33+3q/39fd4x0fPe/4/HUBBjuD71UGZuNjDKkh6k/xJx5X1WomA2uxHTOrvmyA/0scw1bcKqudBRDPfsLGsuJN+j3CV7jUpH0/syyA9ThcRrSEz33MxZOivlkAN/BZUcEK9pHpQ0X90wAm4mFRsYr2vyDWW6GRBvAp/i6kVN34Nk7jBe7hFP7Ik+0kgEZz/Q0b8G8aSB0AEbH9OJOUw9d5Uct5Ph4LsAWf4wqWpO0ZVQGeY2nZEpgDMgLHsQI/4rtG9lUBdmNHxYhnuX+Eu/gfk/BqoHFVgGU420KAkL6ML5JjzIO6ARbjYosBQv9LzMLNVgHEAW81JpSEiUIQFafR4a4tALFjX6fSBWkNjjQIQFsA4r2x4EaXzEAszjspvm0DKDn3XLcuQFaI0qrQNEzOjW1jg6t41udR2zMwPKkmI0sCxCVq02ACxLujbsf9ucz4Hf8MNkCZiaf5tP0TqnPyodUFaKYKRcslzu1lFu427M14SVsyEGf3uDmV2YmjNxTHkFrXwFREqyNvdOxpdGjSlcg7XXYsQET+B2zOSUFHA4zDBczPgMgCmIdfSyzsaBAcqLqR9fpHgyu6ZV+nQGQBTMEejMpbSAOeR4PrZF0AvTqRhegOTMcHWI4Z6OhPKCtwP+NbrMSJghEuav5Xch/+EP8NdG7m/0CjF0bNP5jcY78pOqMC9tFzOp/sE3Ma+ZUFGINryYlzJ75H/LCocyxMGltxt1iHyPpboyxACC1KmrHRCoyd9ByiQVt1RHCiDxRrbljyk6Xv/aCffhWAEPoE+7AKcZGpc8S3vwtHs0SrAvRqj032i6Ils9HcokMR7fWm2vt1AdQZ+UJaXYBC4WqBcTcDLQhqIck3G1utMVx7V1gAAAAASUVORK5CYII="
              alt=""
            />
          </div>
          <Desc v-model="conf.header" />
        </div>
      </div>
      <div>
        <ImageContainer
          :url="conf.teamLogo"
          :height="`${conf.teamLogoHeight}px`"
        />
        <h2>Circuit : {{ trackData.name }}</h2>
        <div class="track">
          <div
            class="image"
            :style="{ backgroundImage: `url(${trackData.image})` }"
          ></div>
          <div class="infos">
            <ul>
              <li><strong>Type de piste :</strong> {{ trackData.type }}</li>
              <li><strong>Surface :</strong> {{ trackData.surface }}</li>
              <li>
                <strong>Longueur du circuit :</strong> {{ trackData.length }}
              </li>
              <li>
                <strong>Plus longue ligne droite :</strong>
                {{ trackData.straightLine }}
              </li>
              <li>
                <strong>Différence d'altitude :</strong>
                {{ trackData.denivele }}
              </li>
              <li><strong>Pluie :</strong> {{ trackData.rain }}</li>
            </ul>
          </div>
        </div>
      </div>
    </header>
    <main>
      <div class="carColumn">
        <h1>Voiture obligatoire : <span v-if="conf.car === null">Non</span></h1>
        <div v-if="carData" class="car">
          <ImageContainer :url="carData.image" height="226px" />
          <div class="head">
            <span class="pp">{{ carData.group }} PP {{ carData.pp }}</span>
            <span class="brand">{{ carData.brand }}</span>
            <span class="name">{{ carData.name }}</span>
          </div>
          <div class="foot"></div>
        </div>
        <textarea
          class="carRestriction"
          v-if="conf.car === false"
          v-model="conf.carRestriction"
        ></textarea>

        <div class="carDetails">
          <div>
            <h4>BOP</h4>
            <select v-model="conf.bop">
              <option value="oui">Oui</option>
              <option value="non">Non</option>
            </select>
          </div>
          <div>
            <h4>Réglages / Tuning</h4>
            <select v-model="conf.tuning">
              <option value="oui">Oui</option>
              <option value="non">Non</option>
            </select>
          </div>
        </div>
      </div>
      <div class="encarts">
        <div class="encart">
          <div class="icon">
            <img
              src="data:image/png;base64,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"
              alt=""
            />
          </div>
          <div class="content">
            <Desc v-model="conf.hours" />
          </div>
        </div>
        <div class="encart">
          <div class="icon">
            <img
              src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADAAAAAwCAYAAABXAvmHAAAAAXNSR0IArs4c6QAABFZJREFUaEPt2WWopVUUBuBnbEHsxq4xUBFFRVEElbHFTmz0hzEqGKNYGCgIioEoBmMgI+YPAwsVMbExxwRjbAzs4pV1huP1zL2nxrlH7oLz5377W3u9e69vrXe9d5wBt3EDHr8xALP6Bsdu4P96AwtgJSxSAL/EO/i234D7mUJL4whsh/Uw+5Bgf8cLuBtX4pN+gOkHgAVxPg7BnG0G9Quuxcn4ps13Wi7rFcDmuBk5/divuAcP4Hl8Xn9fDOtjK2yPOervH2MvPN4tiF4A7IYbMQ/+xFU4Gx+NEMyyOA2H8Xcf+gn74M5uQHQLYFM8jLnwNfbAQx0GsDVuQVLwZ2yBpzr00VUnTmV5BUvhKySNXu1041q/Dh7BQkg6rV0+23bXzQ1cgqPxB7apfG97wxYLJ9R3MxsuxnGdOOsUwHJ4u6rNNZXHnew3o7WpSAcj1WllfNiu03YBrFqOd8eh+A3L17W3u9dw65bBe1WdrsZt1fimjuR8OABxeix2qa7a7Ovealgj+e/keXwmJZst3fuOSq2W1a0VgFSWszAR87aIIHRg1y6qzkhgtsTtmL/Fwh8KxJnVa6YvGQpgcdyKzWpFcnIK7sIz+LTydKRgenmeA1wSG2Jn7FnlOj4fQ9K40SD/UUaD/AmsVbvn6o7H+71E04d3V6zT36l8pWRv0iCGjRtICQvJauTg6TinOmwfYujZReJM905qxxLrjomvAWB/3FAPL8QJPW85cxxcVIUl3vcNDwuA5NybWAEvYgOE+o5GC0UPSUwHT9kdHwBJm5Sw2La4bzRG3hRTYgzjjU0IgAwXh1dTSu0PsxzNlu81nTpc7IoAyJVkgko7T5cdBLsOByX2AEhtT/3PV57KMwiWKpmKNC0A0qwyCh6FywchehyJy9KVAyADSYaKzKcXDAiASTgvs0MAhPGtgktxzIAASKzJmKkBEK4TvvE0Nh4QAIk1XGlKAIR1ZhLKhLUG3hrlIFbD60g5nRgAC+MDzFdD9t6juBck3ggBYaTfZahqcKF8vCfWySelMvNOp6yj5EaiLSX3oyPFIqZNagCYG/eXwpCHURtOqeYWsWpWWkp8Guy5lS2J5dHQiMgxzQNNBKqgytfd0DXfrRdvKu3mvwSSePbDqchMEAvJTK86qQSxlrpQJMAs2qgp2qjLk3E9XprJKNbFATiwSd3OlhG9crjPNe8/3FC/A84oet38Tm4lI2YEqUxwX/QIaNGasKLMZYSMLN9szxZtyBDzL2tHVsn4Ftk88uHQIT/MNbz8tVLnohzklwKQQTySYSzfWKpcgo0QHNa7Zv2SHkPj+LGqTZjyk8MdUDsAGu8ngHDxyCxREEIA+2kB/WDJKJlPvm/HeScAhvpbva4+01GEgPGlJoz0P4JUtWk1BWZAf7lS8Y12Ah66phcAM9pviSKHubGMq9kjqZSUSnn+rJ+NcmYA6OYgu35nDEDXR9enF8duoE8H2bWbgb+BvwAot8yuXsLGbwAAAABJRU5ErkJggg=="
              alt=""
            />
          </div>
          <div class="content">
            <Desc v-model="conf.conditions" />
          </div>
        </div>
        <div class="encart">
          <div class="icon">
            <img
              src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADAAAAAwCAYAAABXAvmHAAAAAXNSR0IArs4c6QAABWZJREFUaEPtmHXIJlUUh5/VtVvXRLEVW0FFRERQUf8wsFBQMTBZO7EV7E5ssbFQMRAVRFQEFQxwF7swdpW1O3nwXJhvdma/O5fxffngOzAw78w7997fyd85ExjjMmGMn59xAMO24LgFxqIF5gFWBVYBVgaWB+YAFgAmAn8CX1euT4B3gQ+B3/sG3MWF1gBOB3YA5i04iMBeA7YBvin4vvGTXAArxOYLA38Bb4dW3wvN/gF8C/wDzAUsCiwGLB5WSpbSQkcDlw8awPXAQcATwP7AtIID7AI8ALwBrA9o0XuAp4ETAnznZXMtMCU2XCsOfxEwf4fd/gb85klgErAtcAuwTKxxI3AI4P86SS6A74AFI1DdeLdOu/z35wuBFeNb11sIeBlYF5gbuA44rKslcgH8DJh9VgLeB/T5A4DfMoBsD+wNXBwZ6iTA9UwEtwN3Aw/F+tcAh3cB0RXAnuG3jwEeLEeOC/cRgOn1fOBOYMew6JWA6z0SIAxwAz1LugLQxNcCNwEHZu0AVQDWAxOCPm8AmxR0nzOBZ+P3fMBlwDE563cFoNvcHNraKWcDRgLQ5+8D7gd2j5ryYBTAY4FXgccjQWix40fbIxfAT+GzW0faewXYeLTF433VAk8BXs8ArqXsGxnJe1Pt98Cj4U5nAGfPap+uAKQPFq+vgCUKANwLCF5Nb1T5PtWZ5P/bAQ8DcwInA+e17ZUL4EdA35TvfApYkb33+WhStcANwDtxrV750ACWouwF3BXPdwYEbPU2HoyLmaQrAIuXGWMLYHPg+dFOX4uBq4CPAQmeJDDJ9KAd1omPKs8FdFsUuLWDwozYsgSA2tAv9VUBmNMVs4m1QrNrLRmqgLWW3Mig9PoyqvlS8d1yAcjnSzcoxMK5XxuHKgHgN/rnlhnaT3+R5O0TwSnp81okXq4DvAm8DmzQsKaZyCp+LnBK/X0JADOSYkDrBh7EdazKWsMqbWxIn38Afg1r+Uwr/RLPtJaiJb4Iir1kfF895xHAFXEdVQrAg+gOuYHbZhyBSseV2SuUQe2vFxpW01Xx0AZwY3HLtUBfADyY7qRU9zZtmhxmi2ps4Mq5JH2HRhbSjU4ctgXUuq6lFUyPVZHwyUjbur1zgFOHDcAMZazYG9u51cUYkK5Ype0VBGlG87l86axSAKZM/d+eQHcqFQPXQNeNzERJ5FdtvMeUfVpoXyuMkNwY6AuAiaBJAWah1J3Vzyj91ve9jIOhArALU/MGp1VXN/LwWkVXaZJLo4g10olcC6SW0gNojVJxUmFTMyOmFq5jTBgbgmmaG0k/JkendnWpBfoCIIN1oiH3MTAVf/vc3z6vS2KqBwOSwSIX6guAXOfzcJvk886YVgNkpzLVuiQu5Djn1lIA+q3uIzETTKksG3T8M8B75SVgE2Az4MWGhe8Imm2dsJceqgXkTtJlKbXTPiUdsFHD0RPYfu4R90MFkMYyH8TI0cOY4831l0TvUFeywzDnqU5BpBtFAPpyIX09zVW9V7aKPttOz9SayF46qC2ss1XJnrS7CEBfQbwm8BbgqNIxpWLjMzUOKRcyZSYQu8YEw2m2I8mZRo+DrgNqUeqcBrxJmzZHuorcx7TqGN4+wSGw4jTvgrr2/T1oABu2TCWSK1moqs2+RTN1Y4mGZ7mQlXHTSJ3ey88lYkdGxySpS5J6Yd1M0xsvqRtLXZpUwXuD2MGWoxVTZ90lVKhat0+2YmsJO7hWabNAygyz+raPd8/FhKN4rTYADp3k3va3as6+NmlCrVWLWXpnobNhsdjpy9Jv+Y0Nipf3Ws5g9H++fyHGM70DKF5w0B/mBvGgz5W93ziAbFX9T38c8xb4F20wUUA9bW6eAAAAAElFTkSuQmCC"
              alt=""
            />
          </div>
          <div class="content">
            <Desc v-model="conf.fuel" />
          </div>
        </div>
        <div class="encart">
          <div class="icon">
            <img
              src="data:image/png;base64,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"
              alt=""
            />
          </div>
          <div class="content">
            <Desc v-model="conf.tires" />
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import tracks from './assets/tracks.json'
import cars from './assets/cars.json'
import Desc from './components/Desc.vue'
import ImageContainer from './components/ImageContainer.vue'
import FileUpload from './components/FileUpload.vue'
import domtoimage from 'dom-to-image-more'
import { saveAs } from 'file-saver'

export default {
  components: { Desc, ImageContainer, FileUpload },
  data () {
    let conf = {
      title: 'Course n°4 | F1 LEGENDS 2023',
      track: '1680768085-200',
      car: '1680769548-491',
      header:
        '<h1>La course en bref</h1><ul><li><p>Monotype</p></li><li><p>2 types de gommes obligatoires</p></li><li><p>Refuel interdit</p></li></ul>',
      hours:
        '<h1>Horaires</h1><ul><li><p>Départ qualif : 21h</p></li><li><p>Pause: 2-5 minutes</p></li><li><p>Départ course : 21h15</p></li></ul>',
      carRestriction: 'Gr.3',
      teamLogoHeight: 60,
      bop: 'oui',
      tuning: 'non',
      teamLogo:
        'data:image/png;base64,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',
      conditions:
        '<h1>Conditions</h1><ul><li><p>Météo : aléatoire</p></li><li><p>Durée : 26 tours</p></li><li><p>Casteur : ERYTHRO_TV</p></li></ul>',
      fuel: '<h1>Carburant</h1><ul><li><p>Consommation qualif : Non</p></li><li><p>Consommation course : x5</p></li><li><p>Vitesse de ravitaillement : Non</p></li></ul>',
      tires:
        '<h1>Pneus</h1><ul><li><p>Obligatoire : 2 gommes course</p></li><li><p>Usure qualif : Non</p></li><li><p>Usure course : x8</p></li></ul>'
    }

    if (window.localStorage) {
      const c = window.localStorage.getItem('conf')
      if (c) conf = JSON.parse(c)
    }

    return {
      conf,
      cars,
      tracks
    }
  },
  watch: {
    conf: {
      handler () {
        if (window.localStorage)
          window.localStorage.setItem('conf', JSON.stringify(this.conf))
      },
      deep: true
    }
  },
  computed: {
    trackData () {
      return this.tracks.find(t => t.id === this.conf.track)
    },
    carData () {
      return this.cars.find(c => c.id === this.conf.car)
    },
    carsByBrand () {
      const brands = {}
      for (let car of this.cars) {
        if (brands[car.brand] === undefined) brands[car.brand] = []
        brands[car.brand].push(car)
      }
      return brands
    },
    tracksByName () {
      const tracks = {}
      for (let track of this.tracks) {
        if (tracks[track.track] === undefined) tracks[track.track] = []
        tracks[track.track].push(track)
      }
      return tracks
    }
  },
  methods: {
    async exportToPng () {
      domtoimage.toBlob(this.$refs.result).then(blob => {
        saveAs(blob, `${this.conf.title}.png`)
      })
    }
  }
}
</script>

<style lang="scss">
body {
  display: flex;
  align-items: center;
  justify-content: center;
}

#conf {
  margin: 20px;

  label {
    display: block;
  }
}

#main {
  background: #131313;
  color: #919495;
  height: 800px;
  width: 1200px;
  display: flex;
  flex-direction: column;

  header {
    flex-shrink: 0;
    display: flex;
    padding: 20px;
    box-shadow: 0px 0px 12px 4px rgba(0, 0, 0, 0.5);
    position: relative;
    z-index: 2;

    .track {
      display: flex;
      align-items: stretch;

      .image {
        background: transparent no-repeat center center;
        background-size: contain;
        margin-right: 5px;
        flex-shrink: 0;
        width: 170px;
      }

      .infos {
        flex-grow: 1;

        ul,
        li {
          list-style: none;
          padding: 0;
          margin: 0;
          font-weight: 700;
          text-transform: capitalize;

          strong {
            text-transform: none;
            color: #5f6062;
            font-weight: 400;
            display: inline-block;
            width: 200px;
            text-align: right;
          }
        }
      }
    }

    > div {
      flex: 1;

      &:first-child {
        margin-right: 10px;
      }

      &:last-child {
        margin-left: 10px;
      }
    }

    h2 {
      font-weight: bold;
    }

    input {
      background: none;
      color: inherit;
      width: 100%;
      border: none;

      &:focus {
        outline: none;
      }

      &.title {
        font-size: 32px;
        font-weight: bold;
        margin: 0 0 20px 0;
      }
    }

    .desc {
      display: flex;

      .desc-editor {
        flex-grow: 1;
      }

      .icon {
        flex-shrink: 0;
        margin: 0 20px 0 0;

        img {
          filter: invert(1);
        }
      }
    }
  }

  main {
    background: #2e2f33;
    flex-grow: 1;
    position: relative;
    z-index: 1;
    display: flex;
    padding: 20px;

    .carColumn {
      flex-shrink: 0;
      width: 400px;
      margin-right: 20px;
    }

    h1 {
      font-weight: 700;
    }

    .carRestriction {
      font-size: 24px;
      font-weight: bold;
      text-align: center;
      border: none;
      color: inherit;
      background: transparent;
      height: 200px;
      width: 100%;

      &:focus {
        outline: none;
      }
    }

    .carDetails {
      display: flex;
      margin-top: 20px;

      > div {
        flex: 1;
        text-align: center;

        h4 {
          font-size: 14px;
          font-weight: 700;
          text-transform: uppercase;
        }

        select {
          text-align: center;
          font-size: 24px;
          font-weight: 700;
          margin: 10px 0;
        }
      }
    }

    select {
      width: 100%;
      background: transparent;
      color: inherit;
      font-size: inherit;
      border: none;
      appearance: none;

      &:focus {
        outline: none;
      }
    }

    .encarts {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      grid-template-rows: repeat(2, 1fr);
      grid-column-gap: 20px;
      grid-row-gap: 20px;
      flex-grow: 1;
    }

    .encart {
      border: 1px solid #919495;
      border-radius: 12px;
      padding: 12px;
      display: flex;
      align-items: center;
      justify-content: center;
      flex-direction: column;

      .icon {
        flex-shrink: 0;
        height: 60px;
        text-align: center;

        img {
          filter: invert(1);
        }
      }

      .content {
        flex-grow: 1;
        width: 100%;

        h1 {
          text-align: center;
        }
      }
    }

    .car {
      position: relative;
      height: 226px;
      width: 400px;

      .head {
        background: #2e2f33;
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 60px;
        line-height: 60px;
        font-size: 20px;

        .brand {
          font-weight: 700;
          margin-right: 10px;
        }

        .pp {
          float: right;
          display: block;
          padding: 5px 15px;
          font-weight: 700;
          font-size: 16px;
          background: #b8b8b8;
          color: #000;
          border-radius: 40px;
          line-height: 30px;
          margin-top: 15px;
        }
      }

      .foot {
        background: #2e2f33;
        position: absolute;
        bottom: 0;
        left: 0;
        width: 100%;
        height: 60px;
        font-size: 20px;
      }
    }
  }
}
</style>
