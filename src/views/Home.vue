
<template>
  <v-container>
    <v-container>
      <h1>What have you achived today?</h1>
      <p>Use this tool to generate a markdown file listing everything you have acomplished in just 4 steps!</p>
      <br>
    </v-container>

    <h2>Step 1 -  Add headings</h2>
    <p>Personalise your daily log with a suitable header and subheading</p>
    <v-container>
      <v-row>
        <v-text-field
          v-model='heading'
          v-on:keyup.enter='updateMarkdown'
          label='Heading'
        />
        <v-spacer></v-spacer>
        <v-text-field
          v-model='subheading'
          v-on:keyup.enter='updateMarkdown'
          label='Subheading'
        />
      </v-row>
    </v-container>
    <br>
    <h2>Step 2 - Enter tasks</h2>
    <p>No help here, you still need to write what you've done!</p>
    <v-container>
      <v-row>
        <ul>
          <li v-for='(task, index) in tasks' :key='index'>
            {{task}}
            <v-btn
              class="mx-4"
              fab
              small
              color="error"
              v-on:click='deleteTask(index)'
            >
              <v-icon>
                mdi-delete
              </v-icon>
            </v-btn>
          </li>
        </ul>
      </v-row>

      <v-row>
        <v-text-field
          v-model='task'
          v-on:keyup.enter='taskSubmitted'
          label='Add Task'
        />
      </v-row>

    </v-container>
    <br>
    <h2>Step 3 - Check</h2>
    <p>Does everything look good? If you're feeling adventerous customise the markdown</p>
    <v-container>
      <v-row>
        <v-col>
          <h3>Editor</h3>
          <p>Warning - Changes made directly to markdown will be overridden when adding tasks or changing headings.</p>
          <br>
          <v-textarea
            auto-grow
            v-model='markdown'
          />
        </v-col>
        <v-col>
          <h3>Live Preview</h3>
          <p>This is how your markdown should look within Bitbucket however there may be small differences.</p>
          <br>
          <div class='markdown' v-html='compiledMarkdown'></div>
        </v-col>
      </v-row>
    </v-container>
    <br>
    <h2>Step 4 - Add to repository!</h2>
    <p>Well done you did it, now download the log and get it commited to your personal repository!!!</p>
    <br>
    <v-row>
      <v-btn v-on:click='downloadMarkdown'>download daily log</v-btn> 
    </v-row>
  </v-container>
</template>
<script>
import marked from 'marked'   
export default {
  data: function () {
    return {
      heading: this.generateHeader(),
      subheading: 'Week x - Sprint x',
      task: '',
      tasks: [],
      filename: '',
      markdown: ''
    }
  },
  created() {
    let date = new Date()
    this.filename = `Daily Log ${date.getDate()}-${date.getMonth()}-${date.getFullYear()}`
  },
  mounted() {
    if (localStorage.tasks && localStorage.tasks.length > 0) {
      console.log('Found tasks in local storage')
      this.tasks = JSON.parse(localStorage.getItem('tasks'));
    } else {
      console.log('No tasks in local storage')
    }
    this.updateMarkdown()
  },
  computed: {
    compiledMarkdown: function () {
      return marked(this.markdown)
    }
  },
  methods: {
    taskSubmitted: function() {
      this.tasks.push(this.task)
      this.task = ''
      this.updateMarkdown()
      localStorage.setItem('tasks', JSON.stringify(this.tasks))
    },
    deleteTask: function(index) {
      this.tasks.splice(index, 1);
      this.updateMarkdown()
    },
    updateMarkdown: function() {
      let tasks = ''
      this.tasks.forEach(task => {
        tasks += '\n- ' + task
      })
      this.markdown = `# ${this.heading}
**${this.subheading}**
## What I did today
${tasks}`
    },
    generateHeader: function() {
      let date = new Date()
      let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"]
      let days = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']

      const getSuffix = date => {
        switch (date.charAt(date.length-1)) {
          case '1':
            return 'st'
          case '2':
            return 'nd'
          case '3': 
            return 'rd'
          default:
            return 'th'
        }
      }
      return `Daily log - ${days[date.getDay()-1]} ${date.getDate()}${getSuffix(date.getDate().toString())} ${months[date.getMonth()]} ${date.getFullYear()}`
    },
    downloadMarkdown: function() {
      this.updateMarkdown()
      let blob = new Blob([this.markdown], {type: 'text/plain'})
      let a = document.createElement('a');
      a.href =  window.URL.createObjectURL(blob)
      a.download = this.filename + '.md'
      a.click()
      this.tasks = []
      this.updateMarkdown()
      localStorage.tasks = []
    }
  }
}
</script>