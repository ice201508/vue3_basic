<template>
  <template v-for="(tag, index) in displayNodes" :key="tag.key">
    <a-tag :closable="true" :color="tag.key === '0' ? '#f50' : provincesKeyList.includes(tag.key) ? '#108ee9' : 'blue'" @close="handleClose(tag, index)">
      {{tag.title}}
    </a-tag>
  </template>
  <a-tree
    checkable
    :selectable="false"
    :tree-data="treeData"
    v-model:checkedKeys="checkedKeys"
    :expanded-keys="expandedKeys"
    @expand="onExpand"
    @check="checkbox"
  >
  </a-tree>
  <a-button style="margin-right: 8px" @click="onReset">重置</a-button>
  <a-button type="primary" @click="onClose">Submit</a-button>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';

const treeData = [
  {
    title: '全国',
    key: '0',
    parentName: '',
    parentKey: '',
    children: [
      {
        title: '北京市',
        key: '1',
        parentName: '',
        parentKey: '0',
        children: [
          {
            title: '朝阳区',
            key: '11',
            parentName: '北京',
            parentKey: '1',
          },
          {
            title: '亦庄区',
            key: '12',
            parentName: '北京',
            parentKey: '1',
          },
          {
            title: '海淀区',
            key: '13',
            parentName: '北京',
            parentKey: '1',
          },
        ],
      },
      {
        title: '上海市',
        key: '2',
        parentName: '',
        parentKey: '0',
        children: [
          {
            title: '徐汇区',
            key: '21',
            parentName: '上海',
            parentKey: '2',
          },
          {
            title: '浦东新区',
            key: '22',
            parentName: '上海',
            parentKey: '2',
          },
        ],
      },
      {
        title: '河北省',
        key: '3',
        parentName: '',
        parentKey: '0',
        children: [
          {
            title: '石家庄市',
            key: '31',
            parentName: '河北省',
            parentKey: '3',
          },
          {
            title: '邯郸市',
            key: '32',
            parentName: '河北省',
            parentKey: '3',
          },
        ],
      },
    ],
  },
];

interface TagDisplayInterface {
  key: string,
  title: string,
  parentName: string,
  parentKey: string,
  checked?: boolean,
  children?: Array<TagDisplayInterface>,
}

const checkedKeys = ref<Array<string>>([]);
const expandedKeys = ref<Array<string>>([]);
const displayNodes = ref<Array<TagDisplayInterface>>([]);
const provincesKeyList = ref<Array<string>>([]);

onMounted(() => {
  (treeData[0] as any).children.forEach((item: TagDisplayInterface) => {
    provincesKeyList.value.push(item.key);
  })
})

const onExpand = (params: string[]) => {
  expandedKeys.value = params;
  // console.log(222, params);
}

// 复选框选中
const checkbox = (checkedKeysParams: any, e: any) => {
  let currentChecked = Object.assign(e.node.$props.dataRef, {checked: e.checked})
  checkedKeys.value = checkedKeysParams;

  //  防止只选择了全国，取消勾选后，显示数组没有清空
  if(checkedKeys.value.length === 0) {
    displayNodes.value = [];
  }

  // 如果选择了全国, 下面的所有省市都不显示，但是要将所有的id传递给后端
  // 如果选择了一个省，则下面的市都不展示

  // 还要考虑是正选(直接选)的全国， 还是(反选)选择所有省自动选择的全国

  // 1. 全国的正选/反选， 显示的tag变化（是否显示下面的子省市）
  if(checkedKeysParams.includes('0')) {
    displayNodes.value = [];
    displayNodes.value.push({
      key: '0',
      title: '全国',
      parentName: '',
      parentKey: ''
    })
    return;
  } else {
    displayNodes.value.splice(displayNodes.value.findIndex((item: TagDisplayInterface) => item.parentKey == ''), 1)
  }

  // 2. 如果选择了全省
  // console.log(provincesKeyList.value)

  e.checkedNodes.forEach(({props}: {props:TagDisplayInterface}) => {
    // 针对省市的点击分别处理
    if(displayNodes.value.find(item => item.key == props.key)) return;

    //  当前处理的key值
    if(props.parentKey === '0') {
      // 当前key值是 省
      displayNodes.value.push({
        key: props.key,
        title: props.parentName ? props.parentName + props.title : props.title,
        parentName: props.parentName,
        parentKey: props.parentKey
      })
    } else {
      // 当前key值是 市, 当前key对应的省没有勾选
      if(!checkedKeys.value.includes(props.parentKey)) {
        displayNodes.value.push({
          key: props.key,
          title: props.parentName ? props.parentName + props.title : props.title,
          parentName: props.parentName,
          parentKey: props.parentKey
        })
      }
    }

    console.log(222, displayNodes.value)

    // 市级别 取消勾选 对应展示发生删除操作
    // if(currentChecked.checked === false) {
    //   if(props.parentKey === '0') {
    //     // 取消勾选操作，取消的是省
    //     displayNodes.value.splice(displayNodes.value.findIndex((item: TagDisplayInterface) => item.key == currentChecked.key), 1)
    //   } else {
    //     // 取消勾选你操作， 取消的是市
    //   }
    //   // displayNodes.value.splice(displayNodes.value.findIndex((item: TagDisplayInterface) => item.key == currentChecked.key), 1)
    // }
  })
  console.log(111, checkedKeys.value, displayNodes.value)
}

const handleClose = (tag: TagDisplayInterface, index: number) => {
  displayNodes.value.splice(index, 1);
  // console.log(222, checkedKeys.value, tag.key, checkedKeys.value.indexOf(tag.key));
  // tag点击删除的逻辑
  // 如果全省都被选中， 取消一个省，就删除对应省下所有市的checked
  // 如果全省被选中， 取消选中一个市， 还需要删除这个市对应的省
  if(tag.parentKey === '0') {
    let tmpProvince : any = treeData[0].children.find(item => item.key === tag.key)

    let cityKeysInProvince = tmpProvince.children.map((item: any) => item.key)

    cityKeysInProvince.forEach((cityItem: string) => {
      let cityIndex = displayNodes.value.findIndex(_ => _.key == cityItem);
      if(cityIndex != -1) {
        displayNodes.value.splice(cityIndex, 1);
      }
    })
  } else {
    // 如果此时已全选，点击x删除的是省下面的市， 需要把全选的省也删除
    let provinceIndex = displayNodes.value.findIndex(_ => _.key === tag.parentKey);
    if (provinceIndex != -1) {
      displayNodes.value.splice(provinceIndex, 1);
      // 还需要将tree中的省也删除掉，才不会勾选
      checkedKeys.value.splice(checkedKeys.value.indexOf(tag.parentKey), 1);
    }
  }

  checkedKeys.value.splice(checkedKeys.value.indexOf(tag.key), 1);
  if (displayNodes.value.length == 0) {
    checkedKeys.value = [];
  }
  console.log(222, checkedKeys.value, displayNodes.value)
}

const onReset = (e: any) => {
  checkedKeys.value = [];
  displayNodes.value = [];
  expandedKeys.value = [];
}

const handleOk = () => {
  // emits('update:visible', false)
}

const handleCancel = () => {
  // emits('update:visible', false)
}

const onClose = () => {
  // visible.value = false;
};
</script>
