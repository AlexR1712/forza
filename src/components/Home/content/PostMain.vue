<template>
 
  
    <div class="">
      
      
      <div v-if="loading" class="columns is-mobile is-centered">
        <div class="column is-narrow spinner" >
          <div class="spinner">
            <circle3 background="#3273dc" size="60px"></circle3>
          </div>
          
        </div>
      </div>
      
      <div v-else class="all">
      
      <div v-for="(post, index) in pages[pageNumber]" :key="index" class="card card-right ">
        <header class="card-header">
          <p class="card-header-title" :class="colorPost (post.category)">
            {{post.title}}
          </p>
        </header>
        <div class="card-content ">
          <div class="content has-text-justified ">
            {{cutString(post.body)}}
          </div>
        </div>
        <footer class="">
          <div class="columns">
            <div class="column is-one-fifth">
              <p class="buttons is-centered">
                <i class="fa fa-share" aria-hidden="true"></i>&nbsp;&nbsp;
                <a class="button is-small is-link is-outlined" 
                    style="border: none;"
                    @click="shareTelegram(post['.key'], post.title)">
                  <span class="icon fa-lg">
                    <i class="fa fa-telegram fa-lg" aria-hidden="true"></i>
                  </span>
                </a>
                <a class="button is-small is-success is-outlined" style="border: none;"
                @click="shareWhatsapp(post['.key'])">
                  <span class="icon fa-lg">
                    <i class="fa fa-whatsapp fa-lg" aria-hidden="true"></i>
                  </span>
                </a>
                <a class="button is-small is-link is-outlined" style="border: none;" href="javascript: void(0);"
                   data-layout="button"
                   @click="shareFacebook(post['.key'], post.title, cutString(post.body))">
                  <span class="icon fa-lg">
                    <i class="fa fa-facebook fa-lg" aria-hidden="true"></i>
                  </span>
                </a>
              </p>
            </div>
            <div class="column">
              <ul class="is-flex metad">
                <li class=" is-size-7">
                  <time datetime="2018-1-1"><strong>{{postDate(post.created)}}</strong></time>
                </li>
                <li class=" is-size-7">
                  Publicado por: <strong class="has-text-weight-semibold" :class="colorPostLink (post.category)">{{post.author}}</strong>
                </li>
                <li class=" is-size-7">
                  Relacionado con: <strong class="has-text-weight-semibold" :class="colorPostLink (post.category)">{{post.category}}</strong>
                </li>
                <li class=" is-size-7">
                  Dirigido a: <strong class="has-text-weight-semibold" :class="colorPostLink (post.category)" >{{subcategoryParse(post.subcategory)}}</strong>
                </li>
              </ul>
            </div>
            <div class="column is-one-quarter">
              <div class="buttons has-addons is-right" style="margin-right: 5%;">
                <router-link :to=" { name: 'PostView', params: { page: pageNumber, key: post['.key'] } }" class=" button see-more" :class="colorPost (post.category)"><span class="icon"><i
                  class="fa fa-plus" aria-hidden="true"></i></i></span>&nbsp; Ver mas
                </router-link>
              </div>
            </div>

          </div>
        </footer>
      </div>
      <nav class="pagination is-centered" role="navigation" aria-label="pagination">
        <button class="pagination-previous button is-link is-outlined" :disabled="pageNumber == 0" @click="prevPage">
          Anterior
        </button>
        <button class="pagination-next button is-link is-outlined" @click="nextPage"
                :disabled="pageNumber == pages.length -1">Siguiente
        </button>
      </nav>
    </div>
  </div>
</template>

<script>
import moment from 'moment'
import {postsRef} from '../../../config'
import editorReadMode from './editor-read'
import sharer from '../../../mixins/sharers'
import { Circle3 } from 'vue-loading-spinner'
export default {
  name: 'PostMain',
  data () {
    return {
      editorReadMode,
      pageNumber: 0,
      count: '',
      keys: [],
      pages: [],
      promises: [],
      size: 7,
      extraRecord: [],
      newPages: [],
      loading: 'true'
    }
  },
  components: {
    Circle3
  },
  firebase: {
    posts: postsRef
  },
  methods: {
    
    colorPost (text) {
      switch (text) {
        case 'Sistemas':
          return 'sistemas'
        case 'Electrica':
          return 'electrica'
        case 'Servicio-Comunitario':
          return 'servicio'
        case 'Pasantias':
          return 'pasantia'
        case 'Economia':
          return 'economia'
        case 'Administracion':
          return 'administracion'
        case 'Enfermeria':
          return 'enfermeria'
        case 'Agronomia':
          return 'agronomia'
        case 'Todo':
          return 'general'
        default:
      }
    },
    colorPostLink (text) {
      switch (text) {
        case 'Sistemas':
          return 'sistemas-link'
        case 'Electrica':
          return 'electrica-link'
        case 'Servicio-Comunitario':
          return 'servicio-link'
        case 'Pasantias':
          return 'pasantia-link'
        case 'Economia':
          return 'economia-link'
        case 'Administracion':
          return 'administracion-link'
        case 'Enfermeria':
          return 'enfermeria-link'
        case 'Agronomia':
          return 'agronomia-link'
        case 'Todo':
          return 'general-link'
        default:
      }
    },
    subcategoryParse (text) {
      switch (text) {
        case 'sist-regulares':
          return 'Regulares'
        case 'sist-egresados':
          return 'Egresados'
        case 'sist-cinu':
          return 'CINU'
        case 'sist-docentes':
          return 'Docentes'
        case 'elec-regulares':
          return 'Regulares'
        case 'elec-cinu':
          return 'CINU'
        case 'elec-docentes':
          return 'Docentes'
        case 'enfer-regulares':
          return 'Regulares'
        case 'enfer-cinu':
          return 'CINU'
        case 'agro-regulares':
          return 'Regulares'
        case 'agro-cinu':
          return 'CINU'
        case 'admin-regulares':
          return 'Regulares'
        case 'admin-cinu':
          return 'CINU'
        case 'econ-regulares':
          return 'Regulares'
        case 'econ-cinu':
          return 'CINU'
        case 'ser-con':
          return 'General'
        case 'pas':
          return 'General'
        case 'todo':
          return 'General'
        default:
      }
    },
    postDate (epoch) {
      if (!epoch) return // if no time return nothing
      return moment(epoch).format('MM/DD/YY - hh:mm')
    },
    cutString (string) {
      let temp = string.replace(/<(?:.|\n)*?>/gm, '')
      let text = temp.replace(/&nbsp;/gi,'');
      return text.substring(0, 370) + '...'
    },
    nextPage () {
      this.pageNumber++
    
    },
    prevPage () {
      this.pageNumber--
      
    },
    cursorPag (acumulador, cursor) {
      this.pages = acumulador || []
      if (this.$route.path === '/') {
        var query = postsRef.orderByKey().limitToLast(this.size + 1)
      } else if (this.$route.path === '/sistemas/regulares') {
        var query = postsRef.orderByChild('subcategory').equalTo('sist-regulares').limitToLast(this.size + 1)
      } else if (this.$route.path === '/sistemas/cinu') {
        var query = postsRef.orderByChild('subcategory').equalTo('sist-cinu').limitToLast(this.size + 1)
      } else if (this.$route.path === '/sistemas/egresados') {
        var query = postsRef.orderByChild('subcategory').equalTo('sist-egresados').limitToLast(this.size + 1)
      } else if (this.$route.path === '/sistemas/docentes') {
        var query = postsRef.orderByChild('subcategory').equalTo('sist-docentes').limitToLast(this.size + 1)
      } else if (this.$route.path === '/electrica/regulares') {
        var query = postsRef.orderByChild('subcategory').equalTo('elec-regulares').limitToLast(this.size + 1)
      } else if (this.$route.path === '/electrica/cinu') {
        var query = postsRef.orderByChild('subcategory').equalTo('elec-cinu').limitToLast(this.size + 1)
      } else if (this.$route.path === '/electrica/docentes') {
        var query = postsRef.orderByChild('subcategory').equalTo('elec-docentes').limitToLast(this.size + 1)
      } else if (this.$route.path === '/enfermeria/regulares') {
        var query = postsRef.orderByChild('subcategory').equalTo('enfer-regulares').limitToLast(this.size + 1)
      } else if (this.$route.path === '/enfermeria/cinu') {
        var query = postsRef.orderByChild('subcategory').equalTo('enfer-cinu').limitToLast(this.size + 1)
      } else if (this.$route.path === '/agronomia/regulares') {
        var query = postsRef.orderByChild('subcategory').equalTo('agro-regulares').limitToLast(this.size + 1)
      } else if (this.$route.path === '/agronomia/cinu') {
        var query = postsRef.orderByChild('subcategory').equalTo('agro-cinu').limitToLast(this.size + 1)
      } else if (this.$route.path === '/administracion/regulares') {
        var query = postsRef.orderByChild('subcategory').equalTo('admin-regulares').limitToLast(this.size + 1)
      } else if (this.$route.path === '/administracion/cinu') {
        var query = postsRef.orderByChild('subcategory').equalTo('admin-cinu').limitToLast(this.size + 1)
      } else if (this.$route.path === '/economia/regulares') {
        var query = postsRef.orderByChild('subcategory').equalTo('econ-regulares').limitToLast(this.size + 1)
      } else if (this.$route.path === '/economia/cinu') {
        var query = postsRef.orderByChild('subcategory').equalTo('econ-cinu').limitToLast(this.size + 1)
      } else if (this.$route.path === '/servicio-comunitario') {
        var query = postsRef.orderByChild('subcategory').equalTo('ser-con').limitToLast(this.size + 1)
      } else if (this.$route.path === '/pasantias') {
        var query = postsRef.orderByChild('subcategory').equalTo('pas').limitToLast(this.size + 1)
      }

      if (cursor) {
        query = query.endAt(cursor)
      }

      return query.once('value').then(function (snaps) {
        var page = []
        snaps.forEach(function (childSnap) {
          var item = childSnap.val()
          item['.key'] = childSnap.key
          page.unshift(item)
        })
        if (page.length > this.size) {
          this.extraRecord = page.pop()
          this.pages.push(page)
          return this.cursorPag(this.pages, this.extraRecord['.key'])
        } else {
          this.pages.push(page)
          this.loading = false
          return Promise.resolve(this.pages)
        }
      }.bind(this))
    }

  },
  computed: {
    
  },
  mixins: [sharer],
  mounted: function () {
    this.cursorPag()
    if(!this.$route.params.page){
      this.pageNumber = 0
    }else{
      this.pageNumber = this.$route.params.page
    }
  }
}
</script>

<style scoped>

  .enfermeria {
    background-color: #bab138;
  }
  .sistemas {
    background-color: #3273dc;
  }
  .general {
    background-color: #ba6138;
  }
  .electrica {
    background-color: #3aba41;
  }
  .economia {
    background-color: #ba3b4e;
  }
  .administracion {
    background-color: #ba3b4e;
  }
  .servicio {
    background-color: #3895ba;
  }
  .pasantia {
    background-color: #38baaa;
  }
  .agronomia {
    background-color: #ba3883;
  }
  .enfermeria-link {
    color: #bab138;
  }
  .sistemas-link {
    color: #3273dc;
  }
  .general-link {
    color: #ba6138;
  }
  .electrica-link {
    color: #3aba41;
  }
  .economia-link {
    color: #ba3b4e;
  }
  .administracion-link {
    color: #ba3b4e;
  }
  .servicio-link {
    color: #3895ba;
  }
  .pasantia-link {
    color: #38baaa;
  }
  .agronomia-link {
    color: #ba3883;
  }
  .see-more{
    color: white;
  }
  .see-more:hover{
    box-shadow: inset 0 0 100px 100px rgba(255, 255, 255, 0.3);
  }
  .spinner {
    margin-top: 4rem;
    height: 100vh;
  }
</style>
