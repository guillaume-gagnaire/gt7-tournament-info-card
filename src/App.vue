<template>
  <div id="conf">
    <label>
      Circuit :
      <select v-model="track">
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
      <select v-model="car">
        <option :value="null">Pas de restriction</option>
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
      <input type="range" v-model="teamLogoHeight" min="0" max="500" />
    </label>
  </div>
  <div id="main">
    <header>
      <div class="padd">
        <input type="text" v-model="title" class="title" />
        <div class="desc">
          <div class="icon">
            <img
              src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADAAAAAwCAYAAABXAvmHAAAAAXNSR0IArs4c6QAAAuxJREFUaEPt2d/rj3cYx/HH16/lR340rBiZmRhlDhwscqLMkWz+AENRziwlJYUlTrY4WaS25GRHI0VJpMmBEwczYiM/a2UtyoiQrrq/9f1+fe77/tw/Pp/vRz7v0/u6Xvf7eV33+3q/39fd4x0fPe/4/HUBBjuD71UGZuNjDKkh6k/xJx5X1WomA2uxHTOrvmyA/0scw1bcKqudBRDPfsLGsuJN+j3CV7jUpH0/syyA9ThcRrSEz33MxZOivlkAN/BZUcEK9pHpQ0X90wAm4mFRsYr2vyDWW6GRBvAp/i6kVN34Nk7jBe7hFP7Ik+0kgEZz/Q0b8G8aSB0AEbH9OJOUw9d5Uct5Ph4LsAWf4wqWpO0ZVQGeY2nZEpgDMgLHsQI/4rtG9lUBdmNHxYhnuX+Eu/gfk/BqoHFVgGU420KAkL6ML5JjzIO6ARbjYosBQv9LzMLNVgHEAW81JpSEiUIQFafR4a4tALFjX6fSBWkNjjQIQFsA4r2x4EaXzEAszjspvm0DKDn3XLcuQFaI0qrQNEzOjW1jg6t41udR2zMwPKkmI0sCxCVq02ACxLujbsf9ucz4Hf8MNkCZiaf5tP0TqnPyodUFaKYKRcslzu1lFu427M14SVsyEGf3uDmV2YmjNxTHkFrXwFREqyNvdOxpdGjSlcg7XXYsQET+B2zOSUFHA4zDBczPgMgCmIdfSyzsaBAcqLqR9fpHgyu6ZV+nQGQBTMEejMpbSAOeR4PrZF0AvTqRhegOTMcHWI4Z6OhPKCtwP+NbrMSJghEuav5Xch/+EP8NdG7m/0CjF0bNP5jcY78pOqMC9tFzOp/sE3Ma+ZUFGINryYlzJ75H/LCocyxMGltxt1iHyPpboyxACC1KmrHRCoyd9ByiQVt1RHCiDxRrbljyk6Xv/aCffhWAEPoE+7AKcZGpc8S3vwtHs0SrAvRqj032i6Ils9HcokMR7fWm2vt1AdQZ+UJaXYBC4WqBcTcDLQhqIck3G1utMVx7V1gAAAAASUVORK5CYII="
              alt=""
            />
          </div>
          <Desc />
        </div>
      </div>
      <div>
        <ImageContainer :url="teamLogo" :height="`${teamLogoHeight}px`" />
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
    <main></main>
  </div>
</template>

<script>
import tracks from './assets/tracks.json'
import cars from './assets/cars.json'
import Desc from './components/Desc.vue'
import ImageContainer from './components/ImageContainer.vue'
import FileUpload from './components/FileUpload.vue'

export default {
  components: { Desc, ImageContainer, FileUpload },
  data () {
    return {
      title: 'Course n°1 / Nom du championnat',
      track: '1680768085-200',
      tracks,
      car: '1680769548-491',
      cars,
      teamLogoHeight: 60,
      teamLogo:
        'data:image/png;base64,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'
    }
  },
  computed: {
    trackData () {
      return this.tracks.find(t => t.id === this.track)
    },
    carData () {
      return this.cars.find(c => c.id === this.car)
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
        margin-right: 20px;
        flex-shrink: 0;
        width: 150px;
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
  }
}
</style>
