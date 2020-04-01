<template>
  <div>
    <v-card>
      <v-card-text>
        <div class="overline">SOURCE: Minify and Optimize</div>
        <p class="display-1 font-weight-regular text--primary" v-text="welcome"></p>
        <div class="text--primary">Original - Clinic Sample</div>
        <vue-json-editor v-model="clinic_sample" :mode="mode" :show-btns="show_buttons" :exapndedOnStart="expanded" @json-change="onJsonChange"></vue-json-editor>
      </v-card-text>
      <v-card-actions v-if="valid_clinic_sample">
        <v-btn text @click="do_optimize">Optimize Now</v-btn>
      </v-card-actions>
      <div v-else>
        <v-card-text>
          <v-alert type="info" v-text="valid_clinic_sample_text"></v-alert>
        </v-card-text>
      </div>
    </v-card>

    <v-card style="margin-top:24px;">
      <v-card-text v-if="optimized">
        <div class="overline">RESULT:</div>
        <p class="display-1 font-weight-regular text--primary" v-text="win_size"></p>
        <div class="text--primary">Optimized - Clinic Sample</div>

        <vue-json-editor v-model="optimized_clinic_sample" :mode="mode" :show-btns="show_buttons" :exapndedOnStart="expanded" @json-change="onJsonChange"></vue-json-editor>
      </v-card-text>
    </v-card>
  </div>
</template>

<script>
import vueJsonEditor from 'vue-json-editor'
 
export default {
  name: "optimizer",
  props: {},
  data: function () {
    return {
      optimized: false,
      valid_clinic_sample: false,
      valid_clinic_sample_text: "Your JSON does not look as a clinic_sample: Paste it above!",
      show_buttons: false,
      expanded: true,
      mode: 'code',
      clinic_sample: {
        "hint": "paste your clinic sample here!"
      },
      optimized_clinic_sample: {}
    }
  },
  computed: {
    welcome() {
      try {
        return this.$store.state.welcome;
      } catch (e) {
        console.error("e", e);
        return null;
      }
    },
    current_variables() {
      try {
        return this.clinic_sample.variables;
      } catch (e) {
        console.error("current_variables", e);
        return null;
      }
    },
    win_size() {
      try {
        var start = Math.round(JSON.stringify(this.clinic_sample).length / 1000);
        var end = Math.round(JSON.stringify(this.optimized_clinic_sample).length / 1000);
        var win = Math.round(100 - (100 / start * end));
        return "Optimized: Start (" + start + "KB) now (" + end + "KB) = win (" + win + "%)";
      } catch (e) {
        console.error("source_size", e);
        return null;
      }
    }
  },
  methods: {
    load_cs: function(sample) {
      this.clinic_sample = this.$store.state.cs.bscl;
      console.log(sample);
    },
    round_one: function(number) {
      return Math.round(number * 10) / 10;
    },
    clean_data_array: function(array_item) {
      array_item.forEach(element => {
        if (Array.isArray(element)) {
          this.clean_data_array(element);  
        } else {
          if (element !== null) {
            try {
              if ('scores' in element) {
                delete element.scores;  
              };
              if ('patients' in element) {
                delete element.patients;  
              };
              if ('statistics' in element) {
                
              //   // element.statistics
              //   this.clinic_sample.variables.forEach(variable => {
              //     const stat = Object.assign({}, element.statistics[variable]);

              //     // Loop Object 
              //     const keys = Object.keys(stat);
              //     console.log(keys);
              //     keys.forEach(key => {
              //       stat[key] = this.round_one(stat[key]);
              //       //stat[key] = null;
              //     });
              //     
              //     // Save
              //     console.error('===>', stat);
              //     element.statistics[variable] = stat;
              //   });

              };
                
            } catch (e) {
                console.error('clean_data_array', e);
            };
          };
        };
      });
    },
    clean_dimensions_array: function(array_item) {
      array_item.forEach(element => {
        if (Array.isArray(element)) {
          this.clean_dimensions_array(element);  
        } else {
          if (element !== null) {
            if ('scores' in element) {
              delete element.scores;  
            };
            if ('patients' in element) {
              delete element.patients;  
            };
          };
        };
      }).bind(this);
    },
    do_optimize: function() {
      var cs = Object.assign({}, this.clinic_sample);
      console.log("-- do_optimize", cs);

      // Delete Unneeded stuff
      if ('location' in cs) {
        delete cs.location;
      };
      if ('id' in cs) {
        delete cs.id;
      };

      // Remove Patient & Scores
      this.clean_data_array(cs.data); 
      // cs.data.forEach(element => {
      //   console.log('element', element);
      //   if (Array.isArray(element)) {
      //      
      //   };
      // });

      // round to one decimal
      // this.round_one();

      // Set to Optimized
      this.optimized_clinic_sample = cs;
      this.optimized = true;
    },
    do_validate: function(cs) {
      console.log("-- do_validate", cs);
      var return_bool = false;
      if ('date' in cs) {
        if ('dimensions' in cs) {
          if (cs.dimensions.length > 0) {
            if ('variables' in cs) {
              if (cs.variables.length > 0) {
                if ('data' in cs) {
                  if (cs.data.length > 0) {
                    return_bool = true;
                    this.mode = 'tree';
                  };
                };
              };
            };
          };
        };
      };
      this.valid_clinic_sample = return_bool;
    },
    onJsonChange (value) {
        console.log('value:', value);
        this.do_validate(value);
    }
  },
  components: {
      vueJsonEditor
    }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
