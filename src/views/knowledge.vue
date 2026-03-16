<template>
    <div>
        <PageHead title="知识文章">
            <template #buttons>
                <el-button type="primary" >新增</el-button>
                <el-button type="primary" >编辑</el-button>
            </template>
        </PageHead>
        <TableSearch :formItem="formItem" @search="handleSearch" />
    </div>
</template>
<script setup>
import { onMounted, ref, reactive } from 'vue';
import PageHead from '@/components/PageHead.vue';
import TableSearch from '@/components/TableSearch.vue';
import { categoryTree , articlePage } from '@/api/admin.js';

const formItem = [
    { comp: 'input', prop: 'title', label: '文章标题', placeholder: '请输入文章标题' },
    { comp: 'select', prop: 'categoryId', label: '文章分类', placeholder: '请选择文章分类' },
    { comp: 'select', prop: 'status', label: '状态', placeholder: '请选择状态', options: [
        {label: '草稿', value: 0},
        {label: '已发布', value: 1},
        {label: '已下架', value: 2},
    ] },
]

//分页参数
const pagination = reactive({
    currentPge: 1,
    size: 10,
    total: 0
})

const handleSearch = async (formData) => {
    console.log(formData, '搜索表单数据');

    const params = {
        ...pagination,
        ...formData
    }

    const data = await articlePage(params);
    console.log(data, '文章列表数据');

}

const categoryMap = reactive({}); 

const categories = ref([]);

onMounted(async() => {
    const data = await categoryTree();

    categories.value = data.map(item => {
        categoryMap[item.id] = item.categoryName;
        return {
            label: item.categoryName,
            value: item.id
        }
    })
    formItem[1].options = categories.value;

    //获取列表
    handleSearch({});
})
</script>

