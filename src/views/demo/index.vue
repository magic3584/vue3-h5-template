
<script>
import { HttpStatusCode } from "axios";
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router";
import { http } from "@/utils/http";

export default {
  setup() {
    const router = useRouter();
    const list = ref([]);
    const totalMap = ref({});
    const loading = ref(false);
    const finished = ref(false);
    const search = ref("");
    const page = ref(1);
    const pageSize = 10;

    onMounted(() => {
      fetchRecords();
    });

    const loadMore = () => {
      page.value += 1;
      fetchRecords();
    };

    const fetchRecords = () => {
      http
        .request({
          url: "https://raw.githubusercontent.com/magic3584/API/master/vant.json",
          method: "get"
        })
        .then(response => {
          console.log(response.data.data);
          var newList = response.data.data;

          console.log("newList length", newList.length);

          if (page.value == 1) {
            list.value = newList;
          } else {
            list.value = list.value.concat(newList);
          }

          console.log("list length:", list.value.length);
          // 加载状态结束
          loading.value = false;

          if (newList.length == 0) {
            finished.value = true;
          }
        })
        .catch(e => {
          // 加载状态结束
          loading.value = false;
          finished.value = true;
        });
    };

    const scrollToTop = () => {
      var element = document.getElementById("bg");
      console.log("element:", element);
      document.getElementById("bg").scrollTop = 0;
    };

    return {
      list,
      totalMap,
      loading,
      finished,
      search,
      scrollToTop,
      loadMore,
      fetchRecords
    };
  }
};
</script>


<template>

  <div
    id='bg'
    style="width: 100vw;height:100vh;padding: 15px;"
    @keydown.enter="fetchRecords"
  >

    <div v-show="list.length!=0">
      <!-- list -->
      <van-list
        v-model:loading="loading"
        :finished="finished"
        finished-text="没有了"
        @load="loadMore"
      >
        <van-cell
          v-for="item in list"
          :key="item"
          class="cell"
        >
          <div style="color:blue">{{ item.index }}</div>

        </van-cell>
      </van-list>
    </div>
  </div>
</template>



<style scoped>
.cell {
  border-top-left-radius: 10px;
  border-top-right-radius: 10px;
  border-bottom-left-radius: 0;
  border-bottom-right-radius: 0;
  padding: 0;
  margin-bottom: 15px;
  border: 1px solid #b6cde7;
  background-color: red;
  height: 100px;
}
</style>