<script setup>
  import { ref, defineProps, computed } from 'vue';
  // 目前的排序選項
  const currentSort = ref('sno');
  // 是否為降冪排序
  const isSortDesc = ref(false);
  
  const props = defineProps({
    internalSearchText: String,
    slicedUbikeStops: Array,
  });
  
  // 排序後的站點資料
  const sortedUbikeStops = computed(() => {
    const slicedUbikeStops = [...props.slicedUbikeStops];
    
    return isSortDesc.value
    ? slicedUbikeStops.sort((a, b) => b[currentSort.value] - a[currentSort.value])
    : slicedUbikeStops.sort((a, b) => a[currentSort.value] - b[currentSort.value]);
  });
  
  // 指定排序
  const setSort = sortType => {
    if (sortType === currentSort.value) {
      isSortDesc.value = !isSortDesc.value;
    } else {
      currentSort.value = sortType;
      isSortDesc.value = false;
    }
  };
  
  const keywordsHighlight = (text, keyword) => {
    if(keyword === '') return text;
    const reg = new RegExp(keyword, 'gi');
    return text.replace(reg, `<span style="color: red;">${keyword}</span>`);
  };
</script>

<template>
    <table class="table table-striped">
      <thead>
        <tr>
          <th @click="setSort('sno')">
            #
            <span v-show="currentSort === 'sno'">
              <i class="fa" :class="isSortDesc ? 'fa-sort-desc' : 'fa-sort-asc'" aria-hidden="true"></i>
            </span>
          </th>
          <th>
            場站名稱
          </th>
          <th>
            場站區域
          </th>
          <th @click="setSort('sbi')" class="pointer">
            目前可用車輛
            <span v-show="currentSort === 'sbi'">
              <i class="fa" :class="isSortDesc ? 'fa-sort-desc' : 'fa-sort-asc'" aria-hidden="true"></i>
            </span>
          </th>
          <th @click="setSort('tot')" class="pointer">
            總停車格
            <span v-show="currentSort === 'tot'">
              <i class="fa" :class="isSortDesc ? 'fa-sort-desc' : 'fa-sort-asc'" aria-hidden="true"></i>
            </span>
          </th>
          <th>
            資料更新時間
          </th>
        </tr>
      </thead>
      <tbody>
        <!-- 替換成 slicedUbikeStops -->
        <tr v-for="s in sortedUbikeStops" :key="s.sno">
          <td>{{ s.sno }}</td>
          <td v-html="keywordsHighlight(s.sna, internalSearchText)"></td>
          <td>{{ s.sarea }}</td>
          <td>{{ s.sbi }}</td>
          <td>{{ s.tot }}</td>
          <td>{{ (s.mday) }}</td>
        </tr>
      </tbody>
    </table>
</template>
