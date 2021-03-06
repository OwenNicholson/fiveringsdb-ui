<template>
    <div class="deck-wizard card">
        <template v-if="step === 0">
            <div class="card-body">
                <h4 class="card-title">Select a clan</h4>
                <h6 class="card-subtitle mb-2 text-muted">You can change later</h6>
                <b-form-radio-group v-model="clan"
                                    :options="clanOptions"
                                    stacked>
                </b-form-radio-group>
            </div>
            <div class="card-footer">
                <a href="#" class="card-link" @click.prevent="step++">Next</a>
            </div>
        </template>
        <template v-if="step === 1">
            <div class="card-body">
                <h4 class="card-title">Select a format</h4>
                <h6 class="card-subtitle mb-2 text-muted">You can change later</h6>
                <b-form-radio-group v-model="format"
                                    :options="formatOptions"
                                    stacked>
                </b-form-radio-group>
            </div>
            <div class="card-footer">
                <a href="#" class="card-link" @click.prevent="step--">Previous</a>
                <a href="#" class="card-link" @click.prevent="chooseFormat">Next</a>
            </div>
        </template>
        <template v-if="step === 2">
            <div class="card-body">
                <h4 class="card-title">Select a role</h4>
                <h6 class="card-subtitle mb-2 text-muted">You can change later</h6>
                <b-form-radio-group v-model="role"
                                    :options="roleOptions"
                                    stacked>
                </b-form-radio-group>
            </div>
            <div class="card-footer">
                <a href="#" class="card-link" @click.prevent="step--">Previous</a>
                <a href="#" class="card-link" @click.prevent="step++">Next</a>
            </div>
        </template>
        <template v-if="step === 3">
            <div class="card-body">
                <h4 class="card-title">Select a stronghold</h4>
                <h6 class="card-subtitle mb-2 text-muted">You can change later</h6>
                <b-form-radio-group v-model="stronghold"
                                    :options="strongholdOptions"
                                    stacked>
                </b-form-radio-group>
            </div>
            <div class="card-footer">
                <a href="#" class="card-link" @click.prevent="step--">Previous</a>
                <a href="#" class="card-link" @click.prevent="submit">Next</a>
            </div>
        </template>
    </div>
</template>

<script>
  import rest from '@/rest';
  import stores from '@/service/storeService';

  export default {
    name: 'builder-wizard',
    props: {
      value: {
        required: true,
      },
    },
    data() {
      const clanOptions = stores.cards().distinct('clan').filter(clan => clan !== 'neutral').sort()
        .map(clan => ({ value: clan, text: this.$t(`clan.${clan}`) }));
      const formatOptions = [
        {value: 'standard', text: this.$t(`format.standard`)},
        {value: 'skirmish', text: this.$t(`format.skirmish`)},
        {value: 'single-core', text: this.$t(`format.single-core`)},
      ];
      return {
        allClanRoles: [],
        format: null,
        clan: null,
        role: null,
        stronghold: null,
        clanOptions,
        formatOptions,
        step: 0,
      };
    },
    computed: {
      strongholdOptions() {
        return stores.cards({ type: 'stronghold', clan: this.clan }).map(card => ({ value: card.id, text: card.name }));
      },
      clanRoles() {
        return this.allClanRoles.filter(clanRole => clanRole.clan === this.clan);
      },
      roleOptions() {
        return stores.cards({ type: 'role' }).map(role => ({
          value: role.id,
          text: role.name
        }));
      },
    },
    methods: {
      submit() {
        this.$emit('input', {
          format: this.format,
          clan: this.clan,
          role: this.role,
          stronghold: this.stronghold,
        });
      },
      chooseFormat() {
        if (this.format === 'skirmish') {
          this.submit()
          return
        } else {
          return this.step++
        }
      },
    },
    created() {
      rest
        .public()
        .get('clan-roles')
        .then((result) => {
          this.allClanRoles = result.records;
        })
        .catch((reason) => {
          this.$notify({
            title: 'Error',
            text: reason,
            type: 'error',
          });
        })
      ;
    },
  };
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
</style>
