<template>
  <a-layout style="padding: 0 24px 24px">
    <a-layout-content
        :style="{ background: '#fff', padding: '24px', margin: 0, minHeight: '280px' }"
    >

      <a-table
          :columns="columns"
          :row-key="record => record.id"
          :data-source="ebooks"
          :pagination="pagination"
          :loading="loading"
          @change="handleTableChange"
      >
        <!-- 电子书封面 -->
        <template #cover="{text: cover}">
          <img v-if="cover" :src="cover" alt="avatar">
        </template>
        <!-- 编辑按钮       -->
        <template v-slot:action="{text, record}">
          <!-- 空格组件 -->
          <a-space size="small">
            <a-button type="primary">
              编辑
            </a-button>
            <a-button type="danger">
              删除
            </a-button>
          </a-space>
        </template>
      </a-table>
    </a-layout-content>
  </a-layout>
</template>

<script>
import {defineComponent, onMounted, ref} from "vue";
import axios from "axios";

export default defineComponent({
  name: "Admin-eBook",
  setup() {
    const ebooks = ref();
    const pagination = ref({
      current: 1,
      pageSize: 2,
      total: 0
    });
    const loading = ref(false);
    const columns = [
      {
        title: '封面',
        dataIndex: 'cover',
        slots: {customRender: 'cover'}
      },
      {
        title: '名称',
        dataIndex: 'name'
      },
      {
        title: '分类一',
        key: 'category1Id',
        dataIndex: 'category1Id'
      },
      {
        title: '分类二',
        key: 'category2Id',
        dataIndex: 'category2Id'
      }, {
        title: '文档数',
        dataIndex: 'docCount'
      }, {
        title: '阅读数',
        dataIndex: 'viewCount'
      }, {
        title: '点赞数',
        dataIndex: 'voteCount'
      }, {
        title: 'Action',
        key: 'action',
        slots: {
          customRender: 'action'
        }
      }
    ];

    /*
    * 数据查询
    * */
    const handleQuery = (params) => {
      loading.value = true;
      axios.get("/ebook/list", params).then((res) => {
        loading.value = false;
        const data = res.data;
        ebooks.value = data.content;

        // 重置分页按钮
        pagination.value.current = params.page;
      });
    };

    /**
     * 点击表格页码时触发
     * @param pagination
     */
    const handleTableChange = (pagination) => {
      console.log("分页参数: " + pagination);
      handleQuery({
        page: pagination.current,
        size: pagination.pageSize
      });
    };

    onMounted(() => {
      handleQuery({})
    });

    return {
      ebooks,
      pagination,
      columns,
      loading,
      handleTableChange
    }

  }
})
</script>

<style scoped>

</style>