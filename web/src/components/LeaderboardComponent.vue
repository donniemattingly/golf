<template>
  <div>
    <weather-component></weather-component>
    <h1> {{tournamentName}}  <span class="light">Leaderboard</span></h1>
    <div
      class="table-options"
      v-on:click="tableCondensed = !tableCondensed">
<!--      <div v-if="!tableCondensed">-->
<!--        Condensed view-->
<!--      </div>-->
<!--      <div v-if="tableCondensed">-->
<!--        Spacious view-->
<!--      </div>-->
      <div v-if="cutLine">
        Cut Line: {{cutLine}}
      </div>
      <div v-if="projectedCutLine">
        Projected Cut Line: {{projectedCutLine}}
      </div>
    </div>
    <table class="table condensed">
      <thead>
      <tr>
        <th scope="col">Pos</th>
        <th scope="col">Name</th>
        <th scope="col">Total</th>
        <th scope="col">Today</th>
        <th scope="col">Penalty</th>
        <th scope="col">Thru</th>
        <th scope="col">Tier</th>
        <th class="d-none d-sm-table-cell" scope="col">Picked By</th>
      </tr>
      </thead>
      <tbody
        v-for="player in players"
        v-bind:class="{increased: player.score_diff > 0, decreased: player.score_diff < 0}"
        v-bind:key="player.tournament_id">
      <tr v-if="player.firstCut">
        <td colspan="8" class="cutLine"> <i class="fas fa-cut"></i>
          <span v-if="cutLine"> Cut Line </span>
          <span v-if="!cutLine"> The following players were cut </span>
          <i class="fas fa-cut"></i></td>
      </tr>
      <tr>
        <td>{{player.position || ''}}</td>
        <td
          class="player-name-cell"
        ><a
          rel="noreferrer"
          target="_blank"
          :href="`https://www.masters.com/en_US/players/player_${player.id}.html`">
          {{player.first_name}} {{player.last_name}}
        </a>
        </td>
        <td>
          {{zeroOr(player.to_par)}}
        </td>
        <td>
          <span v-if="!cutLine && displayPlayerCut(cutLine, player)"><i class="fas fa-cut" ></i></span>
          <span v-if="cutLine || !displayPlayerCut(cutLine, player)">{{zeroOr(player.today)}}</span>
        </td>
        <td>
          {{getPenaltyColumn(player)}}
          <i v-if="getPenaltyColumn(player) < 0" class="fas fa-trophy"></i>
        </td>
        <td>{{getPickThru(player, timeInformation)}}</td>
        <td>{{player.tier || ''}}</td>
        <td class="d-none d-sm-table-cell">
          <participant-names v-bind:participants="getParticipantsForPlayer(player)">
          </participant-names>
        </td>
      </tr>
      </tbody>
    </table>
    <coloring-key></coloring-key>
  </div>
</template>

<script>
import { DisplayUtils } from '../common/displayUtils';
import { mapGetters } from 'vuex';

import ParticipantNameCell from '@/components/ParticipantNameCell.vue';
import ColoringKeyComponent from '@/components/ColoringKeyComponent.vue';
import WeatherComponent from '@/components/WeatherComponent.vue';

export default {
  name: 'LeaderboardComponent',
  components: {
    'participant-names': ParticipantNameCell,
    'coloring-key': ColoringKeyComponent,
    'weather-component': WeatherComponent,
  },
  data() {
    return {
      tableCondensed: false,
    };
  },
  computed: {
    ...mapGetters({
      timeInformation: 'getTimeInformationForActiveTournament',
      tournamentName: 'getTournamentName',
      cutLine: 'getCutLine',
      projectedCutLine: 'getProjectedCutLine',
      players: 'getOrderedPlayers',
      playersToPoolParticipants: 'getPlayersToPoolParticipants',
    }),
  },
  methods: {
    getParticipantsForPlayer(player) {
      if (Object.prototype.hasOwnProperty.call(this.playersToPoolParticipants, player.id)) {
        return this.playersToPoolParticipants[player.id];
      }
      return '';
    },
    ...DisplayUtils,
  },
};
</script>

<style scoped>
  .table-options{
    display: flex;
    justify-content: flex-end;
  }

  .player-name-cell {
    text-align: left;
  }

  .cutLine {
    background-color: var(--cut-line-bg-color);
    color: var(--cut-line-color);
  }
</style>
