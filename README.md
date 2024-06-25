Event Listeners (sample in App.vue)

used to handle user interactions with your application. 

We can listen to DOM events using the `v-on` directive:
`<button v-on:click="increment">{{ count }}</button>`

`Due to its frequent use, v-on also has a shorthand syntax :`
`<button @click="increment">{{ count }}</button>`

You can put the logic inside the event itself if logic is simple enough
`<button @click="count++">{{ count }}</button>`

or you can create a method in the script section, then append it in the event
`<button @click="increment">{{ count }}</button>`
increment is a method, if the method has params, then `increment(val)`