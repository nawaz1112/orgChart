<template lang='pug'>
#person_picker
  i.material-icons.close(v-on:click='close') close
  .vspace
  .nextline Search existing person
  input.search_input(v-model='searchField' placeholder='Search...')
  #search_results(v-if="searchField.length")
    ul
      li(v-if="searchresults.length" v-for="result in searchresults" v-on:click="selectPerson(result)") {{result.name}}
  .vspace
  .nextline or
  .addnew
    button.btn(@click='registerNew()') Add new person

</template>

<script>
import { mapState, mapMutations, mapActions } from 'vuex'
export default {
  components: {},
  props: {
    type: {
      type: String,
      default: ''
    }
  },
  data: function() {
    return {
      searchField: '',
      person: { name: '', function: '', id: '', photo: '' }
    }
  },
  computed: {
    ...mapState(['people', 'selectedPerson', 'activeDepartment']),
    searchresults: function() {
      var res
      if (this.searchField.length < 3) {
        res = [{ name: 'Type at least 3 characters' }]
      } else {
        res = this.people.filter(
          p =>
            p.name
              .toLowerCase()
              .indexOf(this.searchField.toLowerCase()) > -1
        )
        if (!res.length) {
          res = [{ name: 'No matches' }]
        }
      }
      return res
    }
  },
  watch: {},
  mounted: function() {},
  methods: {
    ...mapMutations(['setShowPerson', 'addAssignment', 'addManager']),
    selectPerson: function(person) {
      if (this.type === 'manager') {
        this.addManager({
          department: this.activeDepartment,
          person: person
        })
      } else {
        this.addAssignment({
          department: this.activeDepartment,
          person: person,
          role: ''
        })
      }
      this.searchField = ''
      this.$store.commit('setSelectedPerson', null)
      this.$emit('close')
    },
    close: function() {
      this.$store.commit('setSelectedPerson', null)
      this.$emit('close')
    },
    registerNew: function() {
      this.setShowPerson({
        name: '',
        new: true,
        manager: this.type === 'manager'
      })
      this.$store.commit('setSelectedPerson', null)
      this.$emit('close')
    }
  }
}
</script>
<style scoped>
#person_picker {
  position: absolute;
  top: 200px;
  left: 50px;
  width: 180px;
  height: 120px;
  background-color: lightgrey;
  padding: 10px;
}
.buttons {
  position: absolute;
  left: 0px;
  bottom: 10px;
  padding: 0px 20px;
}
.buttons button {
  margin: 0px 10px;
  cursor: pointer;
}
.search_input {
  width: calc(100% - 30px);
  border-radius: 3px;
  border: none;
  box-shadow: inset 0px 2px 5px grey;
  padding: 4px 10px 4px 10px;
  font-size: 14px;
}
.search_input:focus {
  outline: none;
}
#search_results {
  width: 218px;
  max-height: 500px;
  border: 1px solid lightgrey;
  position: absolute;
  z-index: 1;
  background-color: white;
  overflow-y: scroll;
  overflow-x: hidden;
  margin-left: 0px;
  color: grey;
  font-size: 12px;
  box-shadow: 3px 3px 3px grey;
  border-radius: 3px;
}
ul {
  list-style: none;
  background-color: white;
  padding: 5px;
  text-align: left;
}
li {
  border-bottom: 1px solid lightgrey;
}

li:hover {
  background: lightblue;
  cursor: pointer;
}
.addnew {
  margin: 5px;
}
.addnew input {
  width: 170px;
}
.material-icons.close {
  position: absolute;
  top: 5px;
  right: 5px;
  font-size: 16px;
  cursor: pointer;
}
.btn {
  cursor: pointer;
}
.nextline {
  width: 100%;
  text-align: center;
}
.vspace {
  height: 10px;
}
</style>
