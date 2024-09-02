<template>
  <div>
    <!-- named slots -->
    <h1><slot name=title></slot></h1>
    <slot name=body></slot>

    <!-- no name slot -->
    <slot></slot>

    <!--scoped slot-->
    <slot :msg="msg"></slot>

    <!--scoped named slot-->
    <slot name="scoped" :msgScoped="msgScoped"></slot>

    <!-- slot with default value-->
     <slot>
      <p>There is no passed elemement/value in slot, so this is the default</p>
     </slot>
  </div>
</template>

<script setup>
import {ref} from 'vue';

const msg = ref("This is the message from the child.");
const msgScoped = ref("This is the message from the child (named scoped slot)");

</script>
