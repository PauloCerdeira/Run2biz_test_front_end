<template>
  <div>
    <div>
      <h1>Question 1 - Find Possible Passwords</h1>
      <h2>Pattern rules:</h2>
      <h3>Part 1</h3>
      <ul>
        <li>Password must be between 184759-856920.</li>
        <li>At least two adjacent digits must be the same.</li>
        <li>
          Starting from left to right, digits only increase or stay the same.
        </li>
      </ul>
      <h3>Part 2</h3>
      <ul>
        <li>
          You must have at least one group of exactly two of the same
          characters.
        </li>
      </ul>

      <p>Choose ruleset:</p>
     
      <input v-model="exactlyOneGroup" type="radio" name="exactlyOneGroup" value="0" />
        <label for="html">Part 1</label><br />
     
      <input v-model="exactlyOneGroup" type="radio" name="exactlyOneGroup" value="1" />
        <label for="css">Part 2</label><br />
     
      <br />
      <button @click="findPossiblePasswords()">find possible passwords</button>
      <span v-if="PossiblePasswords.count">
        Possible Passwords: {{ PossiblePasswords.count }}</span>
      <br />
      <textarea id="passwordOutput" readonly rows="10" cols="30">
Passwords Output</textarea>
    </div>

    <div>
      <h1>Question 2 - Calculate Address</h1>

      <span>On a USB drive you've found a document with a large list of numbers and
        in several different places you've found clues as to what the numbers
        mean. Each line represents a command.
      </span>
      <h2>Rules:</h2>

      <ul>
        <li>Commands starting with 20 increase the "address" variable.</li>
        <li>Commands starting with 5 jump to another command.</li>
        <li>
          Commands starting with any other digit do nothing and the next command is executed in succession.
        </li>
      </ul>

      <textarea v-model="commands" rows="10" cols="30"> </textarea> <br />
      <button @click="CalcAddress()">Calculate Address</button>
      <span v-if="CalcAddressReturn">
        Calculated Address: {{ CalcAddressReturn.address }}</span>
      <br />
      <div v-if="CalcAddressReturn">
        <div :style="'color: ' + historyColoring(step)" v-for="step in CalcAddressReturn.history" :key="step.index">
          {{ step.command }} - 
          <span v-if="step.command == '5'"> Skip {{ step.value }} commands </span>
          <span v-else-if="step.command == '20'"> increase Address by {{ step.value }} </span>
          <span v-else-if="step.value == 'Skipped'">{{ step.value }}</span>
          <span v-else> Invalid </span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      exactlyOneGroup: 1,
      PossiblePasswords: {
        count: null,
        list: "",
      },
      CalcAddressReturn: null,
      commands: `25
52
53
202
54
402
203
510
201`,
    };
  },

  // `mounted` is a lifecycle hook which we will explain later
  mounted() { },
  methods: {
    async findPossiblePasswords() {
      this.PossiblePasswords = (
        await axios.get("http://localhost:3000/possiblePasswords", {
          params: { exactlyOneGroup: this.exactlyOneGroup },
        })
      ).data;
      document.getElementById("passwordOutput").value =
        this.PossiblePasswords.list;
    },
    async CalcAddress() {
      this.CalcAddressReturn =(
        await axios.get("http://localhost:3000/calcAddress", {
          params: { commands: this.commands },
        })
      ).data
      console.log(this.CalcAddressReturn);
    },
    historyColoring(step) {
      if (step.value == "Skipped") {
        return "Red"
      }else if (step.command == "5") {
        return "orange"
      } else if (step.command == "20") {
        return "lightGreen"
      } else return "Grey"
    }
  },
};
</script>

<style scoped>
main {
  padding: 10px;
  /* background-color: rgba(0, 0, 255, 0.2); */
  height: 100%;
}

textarea {
  resize: none;
}
</style>
