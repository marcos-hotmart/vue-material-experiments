<template>
  <div class="api-table">
    <table>
      <tr>
        <th v-for="heading in headings">{{ heading }}</th>
      </tr>

      <tr v-for="{ name, type, description, defaults, value, example } in props">
        <td>
          <span class="prop-name">{{ name }}</span>
          <small class="prop-type" v-if="type">{{ type }}</small>
        </td>

        <td>{{ description }}</td>
        <td v-if="defaults || value || example">
          <code>{{ defaults || value || example }}</code>
        </td>
      </tr>
    </table>
  </div>
</template>

<style lang="scss" scoped>
  @import "~vue-material/components/MdAnimation/variables";
  @import "~vue-material/theme/factory";

  $bg-color: md-get-palette-color(grey, 200);
  $border-color: darken($bg-color, 3%);

  .api-table {
    padding-bottom: 16px;
  }

  table {
    width: 100%;
    table-layout: auto;
    border-collapse: collapse;
    border-spacing: 0;
    background-color: #fff;
    font-size: 13px;
    line-height: 18px;
  }

  tr:first-child {
    background-color: $bg-color;
    font-weight: 500;
  }

  td,
  th {
    padding: 8px 16px;
    border: 1px solid $border-color;
    color: md-get-palette-color(grey, 800);
    text-align: left;
    vertical-align: middle;
  }

  th {
    height: 40px;
    color: md-get-palette-color(grey, 700);
    line-height: 23px;

    &:first-child {
      width: 22%;
    }

    &:nth-child(2) ~ &:last-child {
      width: 17%;
    }
  }

  .prop-name,
  .prop-type {
    display: block;
    text-transform: lowercase;
  }

  .prop-type {
    color: md-get-palette-color(grey, 600);
  }

  code {
    color: md-get-palette-color(red, A200)
  }
</style>

<script>
  export default {
    name: 'ApiTable',
    props: {
      props: Array,
      headings: Array
    }
  }
</script>
