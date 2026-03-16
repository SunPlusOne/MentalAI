<template>
    <el-form ref="ruleFormRef" :model="formData">
        <el-row :gutter="24">
           <template v-for="item in formItemAttrs" :key="item.prop">
                <el-col v-bind="item.col">
                    <el-form-item :label="item.label" :prop="item.prop">
                        <component
                            :is="isComp(item.comp)"
                            v-model="formData[item.prop]"
                            :placeholder="item.placeholder"
                        >
                            <template v-if="item.comp === 'select'">
                                <el-option label="全部" value="" />
                                <el-option
                                    v-for="opt in item.options || []"
                                    :key="opt.value"
                                    :label="opt.label"
                                    :value="opt.value"
                                />
                            </template>
                        </component>
                    </el-form-item>
                </el-col>
            </template>
        </el-row>
        <el-row>
            <el-form-item>
                <el-button type="primary" @click="handleSearch">搜索</el-button>
                <el-button @click="handleReset">重置</el-button>
            </el-form-item> 
        </el-row>
    </el-form>
</template>
<script setup>
import { computed, reactive, ref, watchEffect } from 'vue';

const formItemAttrs = computed(() => {
    const { formItem } = props
    return formItem.map((item) => ({
        ...item,
        col: item.col || { xs: 24, sm: 12, md: 8, lg: 6, xl: 6 }
    }))
})

//表单数据
const ruleFormRef = ref()
const formData = reactive({})

const props = defineProps({
    formItem: {
        type: Array,
        default: () => []
    }
})

const emit = defineEmits(['search', 'reset'])

watchEffect(() => {
    props.formItem.forEach((item) => {
        if (!(item.prop in formData)) {
            formData[item.prop] = ''
        }
    })
})

const isComp = (comp) => {
    return {
        input: 'el-input',
        select: 'el-select'
    }[comp]
}

const handleSearch = () => {
    emit('search', { ...formData })
}

const handleReset = () => {
    if (ruleFormRef.value) {
        ruleFormRef.value.resetFields()
    } else {
        Object.keys(formData).forEach((key) => {
            formData[key] = ''
        })
    }

    emit('reset', { ...formData })
    emit('search', { ...formData })
}

</script>