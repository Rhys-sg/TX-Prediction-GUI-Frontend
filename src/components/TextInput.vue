<template>
  <!-- Text Entry Component -->
  <!-- 
      This component allows students to type the promoter sequence into a modified text entry component.
      It automatically populates the compliment strand, includes uneditable "overhang" sequences,
      contains and inserts the default sequence, and tracks differences between the students sequence and 
      the default.
  -->
  <v-container style="padding: 0px;">

    <!-- Base Text Entry Area -->
    <v-textarea 
      v-model="localCloneUpperPromoterSequence"
      hide-details="auto"
      rows="2"
      :rules="[rules.counter]"
      maxlength="36"
      variant="outlined"
      counter
      no-resize
      style="font-family: monospace;"
      prefix="CGAC"
      @mouseover="isHovered = true"
      @mouseleave="isHovered = false"
      @focus="isFocused = true"
      @blur="isFocused = false"
      :disabled="!isCut"
    ></v-textarea>

    <!-- Template Coding Strand-->
    <div class="text-center template_strand_bakground" style="left: 10%; top: 56%; padding: 0px 17.5px;">
      <span v-if="isCut" class="template_strand_text variable_text">{{ lowerPromoterSequence }}</span>
    </div>

    <!-- Render evenly spaced divs -->
    <div
      v-for="(left, index) in divLeftPositions"
      :key="index" class="hl"
      :style="{
          left: left + 'px',
          display: initedDefault && localCloneUpperPromoterSequence.length > 0 && changedBP[index] ? 'block' : 'none' 
        }"
    ></div>

    <!-- Overhang Suffix -->
    <div class="text-center template_strand_overhang_bakground" style="left: 84%; top: 56%; padding: 0px 17.5px;">
      <span
        v-if="isCut"
        :class="['template_strand_overhang_text', { 'focused': isFocused || localCloneUpperPromoterSequence.length > 0}]"
      >
        {{ "CGCC" }}
      </span>
    </div>

    <!-- -35 Label -->
    <div
      v-if="isCut"
      :class="['text-center', 'h10_h35_background', { 'hovered': isHovered, 'focused': isFocused }]"
      style="left: 19.5%; top: 34.75%; padding: 0px 17.5px;"
    >
      <span class="h10_h35_text">-35</span>
    </div>
    
    <!-- -10 Label -->
    <div
      v-if="isCut"
      :class="['text-center', 'h10_h35_background', { 'hovered': isHovered, 'focused': isFocused }]"
      style="left: 68.75%; top: 34.75%; padding: 0px 17.5px;"
    >
      <span class="h10_h35_text">-10</span>
    </div>
  </v-container>
</template>

<script>
export default {
  props: {
    isCut: {
      type: Boolean,
      required: true
    },
    defaultUpdater: {
      type: Boolean,
      required: true
    },
    blankUpdater: {
      type: Boolean,
      required: true
    },
  },
  data() {
    return {
      localCloneUpperPromoterSequence: '',
      lowerPromoterSequence: this.get_complement(this.pCloneUpperPromoterSequence),
      localIsDefault: this.defaultUpdater,
      initedDefault: false,
      defaultCloneUpperPromoterSequence: 'TTTACACTTTATGCTTCCGGCTCGTATGTTGTGTGG',
      isHovered: false,
      isFocused: false,
      rules: {
        required: value => !!value || 'Required.',
        counter: value => value.length <= 36 || 'Max 20 characters',
      },
      changedBP: new Array(36).fill(true),
    };
  },
  computed: {
    divLeftPositions() {
      const totalDivs = 36;
      const startLeft = 61;
      const endLeft = 369;
      const interval = (endLeft - startLeft) / (totalDivs - 1);
      const positions = [];
      for (let i = 0; i < totalDivs; i++) {
        positions.push(startLeft + i * interval);
      }
      return positions;
    }
  },
  watch: {
    defaultUpdater(newVal) {
      this.localCloneUpperPromoterSequence = this.defaultCloneUpperPromoterSequence;
      this.localIsDefault = true;
      this.initedDefault = true;
    },
    blankUpdater(newVal) {
      this.localCloneUpperPromoterSequence = '';
      this.localIsDefault = false;
      this.initedDefault = false;
    },
    localCloneUpperPromoterSequence(newVal) {
      this.lowerPromoterSequence = this.get_complement(newVal);
      this.changedBP = this.trackChanges(this.defaultCloneUpperPromoterSequence, this.localCloneUpperPromoterSequence);
      this.localIsDefault = (this.localCloneUpperPromoterSequence === this.defaultCloneUpperPromoterSequence);
      this.$emit('update:localCloneUpperPromoterSequence', newVal);
    },
  },
  methods: {
    get_complement(strand) {
      if (!strand) {
        return '';
      }
      const complement_key = { 'A': 'T', 'T': 'A', 'C': 'G', 'G': 'C' };
      let complements = '';
      for (let i = 0; strand && i < strand.length; i++) {
        complements += complement_key[strand[i]];
      }
      return complements;
    },
    trackChanges(oldStr, newStr) {
      const length = Math.max(oldStr.length, newStr.length);
      const changes = [];

      for (let i = 0; i < length; i++) {
          changes.push(oldStr[i] !== newStr[i]);
      }

      return changes;
    },
  }
};
</script>

<style scoped>
.h10_h35_background {
  height: 13px;
  position: absolute;
  transform: translate(-50%, -50%);
  background-color: #C7D2E7;
  border: 1px solid #ffffff84;
  padding: 0px 25.5px;
  border-radius: 7px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: border-color 0.1s ease;
}
.h10_h35_background.hovered {
  border-color: #ffffff;
}
.h10_h35_background.focused {
  border: 2px solid #ffffff;
}
.h10_h35_text {
  color: #ffffff;
  font: 12.19px 'Monaco', monospace, sans-serif;
}
.template_strand_bakground {
  height: 13px;
  position: absolute;
  top: 50%;
  left: 0;
  padding: 0px 25.5px;
  border-radius: 7px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.template_strand_text {
  color: #ffffff84;
  font-family: monospace;
}
.template_strand_overhang_bakground {
  height: 13px;
  position: absolute;
  top: 50%;
  left: 0;
  padding: 0px 25.5px;
  border-radius: 7px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.template_strand_overhang_text {
  color: #C7D2E7;
  font-family: monospace;
}
.template_strand_overhang_text.focused {
  color: #ffffff84;
}
.hl {
  position: absolute;
  top: 63%;
  border-left: 9px solid #ffffff;
  height: 1px;
}
</style>
