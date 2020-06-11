<template>
  <table id="rowActivities">
    <tr>
      <td>
        <div class="de-content-right">
          <div
            class="vertical padded"
            v-for="(value, key) in getActivitiesFiltered"
            :key="key"
          >
            {{ value.Title }} <br />{{ showActivityPercents[key].percent }}
          </div>
        </div>
      </td>
    </tr>
  </table>
</template>

<script>
import Vuex from "vuex";
import _ from "lodash";

export default {
  computed: {
    ...Vuex.mapState(["activityPercents", "selectedArea"]),
    ...Vuex.mapGetters(["getActivitiesFiltered", "getSelectedStage"]),

    showActivityPercents() {
      let selectedArea = this.selectedArea;
      let selectedStage = this.getSelectedStage;
      let filterByAreaStage = _.filter(this.activityPercents, function(item) {
        return item.areaID == selectedArea && item.stageID == selectedStage;
      });

      if (filterByAreaStage.length === 0) {
        let empty = [];
        for (let i = 0; i < this.getActivitiesFiltered.length; i++) {
          empty.push({ percent: "?" });
        }
        filterByAreaStage = empty;
      }

      return filterByAreaStage;
    }
  },

  methods: {
    ...Vuex.mapMutations(["setContentWidth", "setLoading"])
  },

  beforeUpdate() {
    if (
      this.getFromRoute === "change-area" ||
      this.getFromRoute === "change-stage"
    ) {
      this.setLoading({
        show: true,
        message: "Setting Activities...",
        width: "60%"
      });
    }
    //console.warn("LOAD: Getting Activities");
  },

  updated() {
    //Fix for IE not stretching parent container when inner container is wide
    if (navigator.userAgent.indexOf("MSIE") > -1) {
      var box = document.getElementById("rowActivities");
      this.setContentWidth(box.offsetWidth);
    }
  }
};
</script>
