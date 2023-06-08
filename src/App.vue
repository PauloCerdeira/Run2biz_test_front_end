<template>
  <div>
    <h1>Question 1 - Find Possible Passwords</h1>
    <span>
      You are a security expert attempting to break into a company which paid
      you to do so. By exploiting security flaws you discover that valid
      passwords must follow a certain pattern.
    </span>
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
        You must have at least one group of exactly two of the same characters.
      </li>
    </ul>

    <div style="text-align: center">
      <p>Choose ruleset:</p>
     
      <input v-model="exactlyOneGroup" type="radio" name="exactlyOneGroup" value="0" />
        <label for="html">Part 1</label>
     
      <input v-model="exactlyOneGroup" type="radio" name="exactlyOneGroup" value="1" />
        <label for="css">Part 2</label>
      <br />
    </div>

    <div class="button-div">
      <button @click="findPossiblePasswords()">find possible passwords</button>
      <span>
        Possible Passwords:

        <span v-if="PossiblePasswords.count">
          {{ PossiblePasswords.count }}</span>
      </span>
    </div>

    <div style="display: flex; justify-content: center">
      <textarea id="passwordOutput" readonly rows="10" cols="30">
  Passwords Output</textarea>
    </div>
  </div>

  <div>
    <h1>Question 2 - Calculate Address</h1>

    <span>On a USB drive you've found a document with a large list of numbers and
      in several different places you've found clues as to what the numbers
      mean. Each line represents a command.
    </span>
    <h2>Rules:</h2>

    <div class="address-rules">
      <div>
        <ul>
          <li>Commands starting with 20 increase the "address" variable.</li>
          <li>Commands starting with 5 jump to another command.</li>
          <li>
            Commands starting with any other digit do nothing and the next
            command is executed in succession.
          </li>
        </ul>
      </div>

      <div>
        <textarea v-model="commands" rows="10" cols="30"> </textarea>
      </div>
    </div>

    <div class="button-div">
      <button @click="CalcAddress()">Calculate Address</button>
      <span>
        Calculated Address:
        <span v-if="CalcAddressReturn">
          {{ CalcAddressReturn.address }}
        </span>
      </span>
    </div>

    <div style="display: flex; justify-content: center" v-if="CalcAddressReturn">
      <div>
        <div :style="historyColoring(step)" v-for="step in CalcAddressReturn.history" :key="step.index">
          <div style="display: inline-block; min-width: 28px">
            {{ step.command }}
          </div>
          <div style="display: inline-block; min-width: 15px">
            -
          </div>
          <span v-if="step.command == '5'">
            Skip {{ step.value }} commands
          </span>
          <span v-else-if="step.command == '20'">
            increase Address by {{ step.value }}
          </span>
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
      this.CalcAddressReturn = (
        await axios.get("http://localhost:3000/calcAddress", {
          params: { commands: this.commands },
        })
      ).data;
    },
    historyColoring(step) {
      if (step.value == "Skipped") {
        return "color: red; text-decoration: line-through;";
      } else if (step.command == "5") {
        return "color: orange";
      } else if (step.command == "20") {
        return "color: lightGreen";
      } else return "color: Grey";
    },
  },
};
</script>

<style scoped>
h1 {
  color: white;
}

h2 {
  color: rgb(223, 223, 223);
}

#app {
  padding: 10px;
  display: flex;
}

#app>div {
  width: 100%;
  max-width: 500px;
  margin: 0px 15px;
}

@media (max-width: 1100px) {
  #app>div {
    width: 100%;
    margin: 0px auto 20px auto;
  }
}

.address-rules {
  display: flex;
}

.address-rules>div:nth-child(2) {
  min-width: 130px;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.button-div {
  display: flex;
  justify-content: space-around;
  align-items: center;
}

.button-div>span {
  color: white;
  vertical-align: middle;
  font-size: 1.2rem;
}

button {
  cursor: pointer;
  padding: 10px 5px;
  background-color: #864879;
  color: white;
  border-radius: 12px;
  border: 2px solid #583050;
  margin: 5px 0px;
  transition: transform 0.3s;
}

button:hover {
  transform: scale(1.08);
}

button:active {
  opacity: 0.7;
  transform: scale(0.9);
}

textarea {
  resize: none;
  border: 2px solid black;
  background-color: #00000073;
  color: white;
  font-family: verdana;
  border-radius: 6px;
  padding: 10px 0px 0px 10px;

}
textarea:focus-visible {
    outline: -webkit-focus-ring-color auto 0px;
}
</style>
