<template>
  <div class="sizes_component_wrapper">
    <span> {{ title }}</span>
    <div class="sizes_block">
      <div
        v-for="item in sizeData"
        :key="item"
        v-on:click="onItemClick(type, item.id, type === 'HEIGTH' ? true : item.isActual)"
        class="sizes_item"
        :class="{
          activeSizeItem:
            type === 'SIZE'
              ? isActiveSize(item.id, type === 'HEIGTH' ? true : item.isActual)
              : isActiveHeigth(item.id, type === 'HEIGTH' ? true : item.isActual)
        }"
      >
        <span>
          {{ item.value }}
        </span>
        <img
          v-if="item.isActual === false"
          class="remind_icon"
          src="../../../assets/remind.svg"
          alt="remind"
        />
      </div>
    </div>
  </div>
</template>

<script>
import { mapMutations, mapGetters } from 'vuex'

export default {
  props: {
    title: String,
    sizeData: Array,
    type: String,
    isInModal: Boolean
  },
  data() {
    return {
      localActiveHeigth: null,
      localActiveSize: []
    }
  },

  methods: {
    ...mapMutations({
      setActiveSizes: 'setActiveSizes',
      setModalData: 'setModalData'
    }),
    isActiveSize(currentItemId, isActual) {
      if (isActual) {
        const foundElement = this.isInModal
          ? this.localActiveSize.find((elem) => elem === currentItemId)
          : this.activeItems.items.activeSize.find((elem) => elem === currentItemId)
        return !!foundElement
      }
    },
    isActiveHeigth(currentItemId, isActual) {
      if (isActual) {
        return this.isInModal
          ? this.localActiveHeigth === currentItemId
          : this.activeItems.items.activeHeigth === currentItemId
      }
    },
    onItemClick(type, id, isActual) {
      if (isActual) {
        if (type === 'SIZE') {
          let foundElement = this.isActiveSize(id, isActual)
          if (this.isInModal) {
            foundElement
              ? (this.localActiveSize = this.localActiveSize.filter((elem) => elem !== id))
              : (this.localActiveSize = [...this.localActiveSize, id])

            this.$emit('saveData', this.localActiveSize, null)
          } else {
            foundElement
              ? this.setActiveSizes({
                  activeSize: this.activeItems.items.activeSize.filter((elem) => {
                    return elem !== id
                  }),
                  activeHeigth: this.activeItems.items.activeHeigth
                })
              : this.setActiveSizes({
                  activeSize: [...this.activeItems.items.activeSize, id],
                  activeHeigth: this.activeItems.items.activeHeigth
                })
            this.setModalData()
          }
        } else if (type === 'HEIGTH') {
          if (this.isInModal) {
            this.localActiveHeigth === id
              ? (this.localActiveHeigth = null)
              : (this.localActiveHeigth = id)

            this.$emit('saveData', [], this.localActiveHeigth)
          } else {
            this.setActiveSizes({
              activeSize: this.activeItems.items.activeSize,
              activeHeigth: this.activeItems.items.activeHeigth === id ? null : id
            })
            this.setModalData()
          }
        }
      }
    }
  },
  computed: {
    ...mapGetters({
      product: 'product',
      activeItems: 'activeItems'
    })
  }
}
</script>

<style>
.sizes_component_wrapper:first-child {
  margin-top: 20px;
}
.sizes_block {
  position: relative;
  display: flex;
  align-items: center;
  margin: 10px 0 30px;
}
.sizes_item {
  min-width: 65px;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid;
  border-color: #b0afab;
  border-radius: 7px;
  padding: 10px 0;
  margin-right: 10px;
  cursor: pointer;
  transition: 0.4s;
}
.activeSizeItem {
  border-color: #bd9365;
  background-color: #bd9365;
}
.activeSizeItem span {
  color: white;
}
.remind_icon {
  position: absolute;
  top: -5px;
  right: -4px;
  background-color: white;
}

@media screen and (max-width: 1050px) {
  .sizes_block {
    height: 60px;
    position: relative;
    overflow-x: scroll;
  }
  ::-webkit-scrollbar {
    width: 100%;
    height: 3px;
  }
  ::-webkit-scrollbar-thumb {
    background: #282828;
  }
}
</style>
