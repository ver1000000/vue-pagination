# vue-pagination
A simple Vue slot-based component for pagination.

### Pagination.vue
A row of buttons [<][1][2][3][...][>].

### Paginated.vue
A wrapper for items which includes Pagination.vue. Just provide your items to `:items` property.
```
<paginated :items="posts" :per-page="10">
  <ul slot-scope="page" v-for="post in page.items">
    <li>{{ post.title }}</li>
  </ul>
</paginated>
```
