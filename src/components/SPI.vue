<template>
  <div>
    <div class="nav">
        <div class="branch">
        <label>Branch 📚</label>
        <button @click="selectedBranch='cse'"
                :class="{active: selectedBranch === 'cse' }" > CSE 💻
        </button>
        <button @click="selectedBranch='ece'"
                :class="{active: selectedBranch === 'ece' }"> ECE 💡
        </button>
        <button @click="selectedBranch='me'"
                :class="{active: selectedBranch === 'me' }"> ME 🤖
        </button>
        </div>
    <div class="semester">
        <label>Semester 🎉</label>
        <select v-model.number="selectedSemester">
        <option v-for="i in 8" :value="i-1" :key="i">Semester {{i}} 👉🏾 </option>
        </select>
        </div>
    </div>
    <table class="course-list">
    <tr v-for="(course,index) in course[selectedSemester]" :key="course.id" class="course">
      <td class="name"> {{course.courseCode}} {{course.courseName}}</td>
        <td>
          <input type="text" placeholder="Course Grade" v-model="courseGrades[index]" >
        </td>
    </tr>
    </table>
    <h6 class='ss'> <span class="note">Note :</span> Now you can also opt for SS Grade 😉     </h6>
    <hr v-if="totalSPI">
    <div class="result" v-if="totalSPI">
    <h3>{{tweenSPI}}<span class="outof" v-if="totalSPI">/10</span></h3>
    <h4>{{captions}}</h4>
    </div>
  </div>
</template>

<script>

import cse from '../../static/cse.json';
import ece from '../../static/ece.json';
import me from '../../static/me.json';

export default {
  name: 'SPI',
  data() {
    return {
      cse,
      ece,
      me,
      selectedBranch: 'cse',
      selectedSemester: 0,
      courseCredits: [],
      courseGrades: [],
      tweenedNumber: 0,
    };
  },
  methods: {
    getScore(grade) {
      switch (grade) {
        case 'O':
        case 'o':
          return 10;
        case 'A+':
        case 'a+':
          return 10;
        case 'A':
        case 'a':
          return 9;
        case 'B+':
        case 'b+':
          return 8;
        case 'B':
        case 'b':
          return 7;
        case 'C+':
        case 'c+':
          return 6;
        case 'C':
        case 'c':
          return 5;
        case 'D+':
        case 'd+':
          return 4;
        case 'D':
        case 'd':
          return 3;
        case 'F':
        case 'f':
          return 2;
        case 'SS':
          return 0;
        case 'ss':
          return 0;
        default:
          return 0;
      }
    },
    calc(num) {
      let numstr = num.toString();
      numstr = numstr.slice(0, numstr.indexOf('.') + 4);
      return Number(numstr);
    },
  },
  computed: {
    course() {
      return this[this.selectedBranch];
    },
    computeCourseCredits() {
      this.course[this.selectedSemester].forEach((el) => {
        const { courseCredits } = this;
        courseCredits.push(el.courseCredits);
      });
      return this.courseCredits;
    },
    captions() {
      if (this.totalSPI <= 10 && this.totalSPI > 8.5) {
        return 'Can expect to go to JAPAN 🇯🇵 🤓';
      } if (this.totalSPI <= 8.5 && this.totalSPI > 7.8) {
        return ' Macchaa! Rocked it 😎';
      } if (this.totalSPI <= 7.8 && this.totalSPI > 7) {
        return ' Cool, great score 🥂 ';
      } if (this.totalSPI <= 7 && this.totalSPI > 6) {
        return 'Needs to put extra effort 🔨';
      } if (this.totalSPI <= 6) {
        return 'Padh lo thoda bro 😐';
      }
      return 'It Seems, you have entered the wrong value ❌';
    },
    semTotal() {
      let score = 0;
      let tCredits = 0;
      let totalCredits = 0;
      let estimated = 0;
      tCredits = this.computeCourseCredits;
      this.courseGrades.forEach((el, index) => {
        const grade = this.getScore(el);
        if (grade !== 0) {
          totalCredits += tCredits[index];
        }
        score += grade * this.courseCredits[index];
        estimated = score / totalCredits;
      });
      return estimated;
    },
    tweenSPI() {
      return this.tweenedNumber.toFixed(1);
    },
    // totalCredits() {
    //   const tCredits = this.computeCourseCredits;
    //   let totalCredits = 0;
    //   tCredits.forEach((el) => {
    //     totalCredits += el;
    //   });
    //   return totalCredits;
    // },
    totalSPI() {
      const spi = this.semTotal;
      return spi === 0 ? null : this.calc(spi);
    },
  },
  watch: {
    selectedSemester() {
      this.courseGrades = [];
      this.courseCredits = [];
    },
    selectedBranch() {
      this.courseGrades = [];
      this.courseCredits = [];
    },
    totalSPI(newValue) {
      window.TweenLite.to(this.$data, 1.8, { tweenedNumber: newValue });
    },
  },
};

</script>

<style scoped lang="scss">
@import '../styles/home.scss';

</style>
