<template>
  <ul class="nav-mobile">
    <li></li>
    <li class="menu-container">
      <input id="menu-toggle" type="checkbox">
      <label
         for="menu-toggle"
         class="menu-button"
         :class="{ 'remove-after': !isActive, 'put-after': isActive }"
         @click="isActive = !isActive"
      >
        <!-- navbar svg component -->
        <NavMobileSvg />
      </label>
      <ul
        class="menu-sidebar"
        :class="{ 'menu-close': !isActive, 'menu-open': isActive }"
      >
        <li
          v-for="(item, index) in menus"
          :key="index"
          v-if="item.title !== 'SOLUÇÕES'"
          @click="isActive = !isActive"
        >
          <nuxt-link :to="`${item.url}`">
            {{ item.title }}
          </nuxt-link>
        </li>
        <li v-else>
          <input type="checkbox" id="sub-one" class="submenu-toggle">
          <label class="submenu-label" for="sub-one">{{ item.title }}</label>
          <div class="arrow right">&#8250;</div>
          <ul class="menu-sub">
            <li class="menu-sub-title">
              <label class="submenu-label" for="sub-one">Voltar</label>
              <div class="arrow left">&#8249;</div>
            </li>
            <li
              v-for="(item, index) in subMenu"
              :key="index"
              @click="isActive = !isActive"
            >
              <nuxt-link :to="`${item.url}`">
                {{ item.title }}
              </nuxt-link>
            </li>
          </ul>
        </li>
        <!--
        <li>
          <input type="checkbox" id="sub-one" class="submenu-toggle">
          <label class="submenu-label" for="sub-one">Category</label>
          <div class="arrow right">&#8250;</div>
          <ul class="menu-sub">
            <li class="menu-sub-title">
              <label class="submenu-label" for="sub-one">Back</label>
              <div class="arrow left">&#8249;</div>
            </li>
            <li><a href="#">Sub-item</a></li>
            <li><a href="#">Sub-item</a></li>
            <li><a href="#">Sub-item</a></li>
            <li><a href="#">Sub-item</a></li>
          </ul>
        </li>
        <li>
          <input type="checkbox" id="sub-two" class="submenu-toggle">
          <label class="submenu-label" for="sub-two">Category</label>
          <div class="arrow right">&#8250;</div>
          <ul class="menu-sub">
            <li class="menu-sub-title">
              <label class="submenu-label" for="sub-two">Back</label>
              <div class="arrow left">&#8249;</div>
            </li>
            <li><a href="#">Sub-item</a></li>
            <li><a href="#">Sub-item</a></li>
            <li><a href="#">Sub-item</a></li>
            <li><a href="#">Sub-item</a></li>
          </ul>
        </li>
        -->
      </ul>
    </li>
  </ul>
</template>

<script>
  import API from '../services/API';
  import NavMobileSvg from "./NavMobileSvg";

  export default {
    name: 'NavMobile',
    components: {
      NavMobileSvg
    },

    data() {
      return {
        menus: [],
        subMenu: [],
        isActive: false,
      }
    },

    created() {
      this.getAllMenu();
    },

    methods: {
      getMenu2() {
        return API.get('/menus/v1/menus/header_menu/');
      },

      getSubMenu2() {
        return API.get('/menus/v1/menus/sub_menu/');
      },

      async getAllMenu() {
        this.loading = true;

        try {
          await API.all([this.getMenu2(), this.getSubMenu2()])
            .then(API.spread((menu, subMenu) => {
              this.menus = menu.data.items;
              this.subMenu = subMenu.data.items;
            }));
        } catch (e) {
          this.error = true;
        }

        this.loading = false;
      },
    }
  }
</script>

<style lang="scss">
  .nav-mobile {
    display: none;
    background: #7B451A;
    color: #FFF;
    padding: 0;
    margin: 0;
    cursor: auto;
    font-size: 18px;
    list-style-type: none;
    box-shadow: 0 5px 5px -5px #333;
    &:after { content: ""; display: table; clear: both; }
    svg {
      height: 45px;
      width: 65px;
      padding: 9px;
      path { fill: #fff; }
      &.icon-close {
        display: none;
        padding: 15px;
      }
    }
    li {
      width: 100%;
      height: 45px;
      line-height: 46px;
      text-align: center;
      float: left;
      color: #FFF;
      a {
        display: block;
        color: #FFF;
        width: 100%;
        height: 100%;
        text-decoration: none;
        text-transform: uppercase;
      }
    }
    .menu-button {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      margin: 0;
      cursor: pointer;
      display: block;
      &:after {
        opacity: 0;
        top: 0;
        content: "";
        width: 100vw;
        display: block;
        position: fixed;
        height: 100vh;
        background: rgba(0,0,0,0.5);
        content: "";
        pointer-events: none;
        transition: opacity 0.2s cubic-bezier(0,0,0.3,1);
        transition-delay: 0.1s;
      }
    }
    #menu-toggle {
      display: none;
      &.active ~ .menu-button,
      &:checked ~ .menu-button {
        //.icon-close { display: block; }
        //.icon-open { display: none; }
        &:after {
          opacity: 1;
          pointer-events: auto;
          transition: opacity 0.3s cubic-bezier(0,0,0.3,1);
          z-index: 9;
        }
      }
      &.active ~ .menu-sidebar,
      &:checked ~ .menu-sidebar {
        transform: translateX(0);
        transition: transform 0.3s cubic-bezier(0,0,0.3,1);
      }
    }
    .menu-container {
      width: 65px;
      float: left;
      cursor: pointer;
      position: absolute;
      .menu-sidebar {
        box-shadow: 5px 0 5px -5px #333;
        display: block;
        width: 65vw;
        bottom: 0;
        background: #7B451A;
        color: #333;
        position: fixed;
        //transform: translateX(-405px);
        transition: transform 0.3s cubic-bezier(0,0,0.3,1);
        top: 0;
        z-index: 10;
        list-style-type: none;
        padding: 0;
        max-width: 400px;
        .arrow {
          position: absolute;
          line-height: 50px;
          font-size: 32px;
          color: #FFF;
          top: 0;
          z-index: 0;
          &.left { left: 25px; }
          &.right { right: 25px; }
        }
        li {
          height: 55px;
          line-height: 55px;
          font-size: 16px;
          text-align: left;
          position: relative;
          border-bottom: 1px solid rgba(0,0,0,0.1);
          padding-left: 20px;
          &:hover { background: inherit; }
          .menu-sub {
            position: fixed;
            top: 0;
            right: 0;
            bottom: 0;
            width: 0;
            overflow: hidden;
            background: #7B451A;
            visibility: hidden;
            transition: all 0.3s cubic-bezier(0,0,0.3,1);
            //border-left: 1px solid #ccc;
            list-style-type: none;
            padding: 0;
            margin: 0;
            z-index: 2;
            max-width: 400px;
            li { overflow: hidden; }
            .menu-sub-title { padding-left: 50px; }
          }
          .submenu-label {
            cursor: pointer;
            width: 100%;
            height: 100%;
            display: block;
          }
          .submenu-toggle {
            display: none;
            &.active ~ .menu-sub,
            &:checked ~ .menu-sub {
              width: 65vw;
              visibility: visible;
              z-index: 1;
              transition: width 0.35s cubic-bezier(0,0,0.3,1);
            }
          }
        }
      }
    }
  }

  label {
    color: #FFF;
    font-weight: 400;
  }

  .nav-mobile .menu-open {
    -webkit-transform: translateX(0) !important;
    -moz-transform: translateX(0) !important;
    -ms-transform: translateX(0) !important;
    -o-transform: translateX(0) !important;
    transform: translateX(0) !important;
  }

  .nav-mobile .menu-close {
    -webkit-transform: translateX(-405px) !important;
    -moz-transform: translateX(-405px) !important;
    -ms-transform: translateX(-405px) !important;
    -o-transform: translateX(-405px) !important;
    transform: translateX(-405px) !important;
  }

  .nav-mobile.remove-after:after,
  .nav-mobile #menu-toggle:checked ~ .remove-after:after{
    opacity: 0!important;
    pointer-events: auto;
    transition: opacity 0.3s cubic-bezier(0, 0, 0.3, 1);
    z-index: 9;
    display: none!important;
  }

  .nav-mobile .put-after:after {
    opacity: 1;
    pointer-events: auto;
    transition: opacity 0.3s cubic-bezier(0, 0, 0.3, 1);
    z-index: 9;
    display: block!important;
  }

  @media (max-width: 768px) {
    .nav-mobile {
      display: block;
    }
  }
</style>
