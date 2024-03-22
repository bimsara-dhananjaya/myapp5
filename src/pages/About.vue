<template>
  <div>
    <h1>Code Page</h1>
    <q-code lang="html">
      <template v-slot:default>
        <!-- Your HTML code snippet here -->
        <div>
          <p>This is a code snippet</p>
        </div>
      </template>
    </q-code>
    <q-code lang="javascript">
      <template v-slot:default>
        // Your JavaScript code snippet here console.log('Hello, world!');
      </template>
    </q-code>
  </div>
</template>

<script>
export default {
  name: "CodePage",
};
</script>
