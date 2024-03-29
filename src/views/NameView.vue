<script setup>
import { ref, watch } from 'vue'
import FontSelector from '@/components/FontSelector.vue'
import MSOIcon from '@/components/icons/MSOIcon.vue'
import PositionSelector from '@/components/PositionSelector.vue'

const finalUrl = ref('');
const selectFinalUrl = () => document.querySelector('.final-url').select();

const nickname = ref('');
const pronouns = ref('');

const addPos = ref(false);
const position = ref('tl');

const addServ = ref(false);
const service = ref('tw');
const availableServices = {
  tw: 'Twitch',
  yt: 'YouTube'
}

const changeFont = ref(false);
const font = ref('fbl');

watch([nickname, pronouns, addPos, position, addServ, service, changeFont, font], (values) => {
  let nickname = values[0].replace(/\W/g, '');
  let pronouns = values[1].replaceAll('/', '-').replace(/[^\w-]/g, '');
  let query = [];
  if (values[4]) query.push('s=' + values[5]);
  if (values[2]) query.push('p=' + values[3]);
  if (values[6]) query.push('f=' + values[7]);
  finalUrl.value = 'https://ovrly.me/name/' + nickname + (pronouns.length ? '/' + pronouns : '') + (query.length ? '?' + query.join('&') : '');
});
</script>

<template>
  <section>
    <h1>Name Overlay</h1>

    <p>If it looks familiar, it's because I made the one on the MSL website, too! Here's the basic example: 
      <em><RouterLink to="/name/YourNickname" target="_blank">https://ovrly.me/name/YourNickname</RouterLink></em>. 
      Simple, efficient, non-intrusive. But there's a few extra bits you can add to make it better..  
    </p>

      <h3><em>URL</em> Generator</h3>

    <p>
      <input type="text" v-model="nickname" class="half-wide" id="nickname" placeholder="Your username / nickname"/>
      <input type="text" v-model="pronouns" class="half-wide" id="pronouns" placeholder="Your pronouns, e.g. she/her (optional)"/>
    </p>

    <p>
      <input type="checkbox" v-model="addServ" id="addServ"/>
      <label for="addServ">Show a streaming service icon?</label>
    </p>

    <p v-if="addServ">
      <ul class="serviceList">
        <li v-for="(label, code) in availableServices" :key="code">
          <input type="radio" name="service" v-model="service" :id="'service_'+code" :value="code"/>
          <label :for="'service_'+code" v-html="label"></label>
        </li>
      </ul>
    </p>

    <p>
      <input type="checkbox" v-model="addPos" id="addPos"/>
      <label for="addPos">Set on-screen position of text?</label>
    </p>

    <PositionSelector v-if="addPos" v-model="position" />

    <p>
      <input type="checkbox" v-model="changeFont" id="changeFont"/>
      <label for="changeFont">Change the overlay font?</label>
    </p>

    <FontSelector v-if="changeFont" v-model="font" />

    <p>
      Your overlay URL:
      <input type="text" class="final-url" v-model="finalUrl" v-on:focus="selectFinalUrl" />
    </p>

    <h3>Here's the nitty gritty!</h3>

    <ul>
      <li>Username (part of url)<span class="inline-icon text-danger"><MSOIcon>star</MSOIcon></span>: let people know what to call you
        <ul>
          <li><strong>/Nickname</strong> - your nickname or username</li>
        </ul>
      </li>
      <li>Pronouns (part of url): let viewers (and commentators) know your preferred pronouns
        <ul>
          <li><strong>/pro-nouns</strong> - your pronouns separated with a dash</li>
        </ul>
      </li>
      <li>Service icon (query parameter): remind everyone which streaming service you're on
        <ul>
          <li v-for="(label, code) in availableServices" :key="code"><strong>s={{ code }}</strong> - {{ label }}</li>
        </ul>
      </li>
      <li>Text position on screen (query parameter): either <strong>t</strong> or <strong>b</strong> for top/bottom, then <strong>l</strong>, <strong>c</strong> or <strong>r</strong> for left/center/right
        <ul>
          <li><strong>p=tl</strong> - Top left (default)</li>
        </ul>
      </li>
    </ul>

  </section>
</template>


<style scoped lang="scss">
.half-wide,
.final-url {
  width: 100%;
  font-size: 1em;
  padding: 0.4em 1em;
}

.half-wide { width: calc(50% - 1em); display: inline-block; }

input[type="checkbox"],
input[type="radio"] {
  &:has(+ label) {
    margin-right: 0.8em;
    cursor: pointer;

    + label {
      cursor: pointer;
    }
  }
}

ul.serviceList {
  display: flex;
  flex-flow: row wrap;
  align-items: start;

  li {
    list-style: none;
    flex: 0 1 33%;
  }
}
</style>
