<template>
  <q-page class="flex column">
    <div class="col q-pt-lg q-px-md">
      <q-input
        bottom-slots
        v-model="search"
        placeholder="location"
        dark
        borderless=""
        >
        <template v-slot:before>
          <q-icon name="my_location" />
        </template>


        <template v-slot:append>
          <q-btn round dense flat icon="search" />
        </template>
      </q-input>
    </div>
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data(){
    return{
      search: ''
    }
  }
}
</script>

<style lang="sass">
  .q-page
    background: linear-gradient(to top, #00b4db, #0083b0)
</style>
