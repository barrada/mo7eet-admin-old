<template>
  <div id="content" >
      <h1>{{type}}</h1> {{excert}}
      <form type="post" id="post-form" v-on:submit.prevent action="" style="width:800px;margin:auto" dir="rtl">        
         <input type="text" v-model="title" name="title" id="title" placeholder="title"  style="width:100%;margin-bottom:2px"  class="text">
         <input type="text" v-model="slug"  name="slug" id="title" placeholder="slug"  style="width:100%;margin-bottom:2px"  class="text">
               <input type="text" v-model="thumbnail" name="thumbnail" id=""  style="width:320px;float:left" placeholder="thumbnail"  class="text">      
        <input type="text" v-model="type" name="type" value="sports" id="" style="width:350px;float:right"  class="text">
         <input type="text" v-model="tags" name="tags" id="" placeholder="tags" style="width:350px;float:right;margin-bottom:2px"  class="text">
         <input type="text" v-model="keywords" name="keywords" id="" placeholder="keywords" style="width:320px;float:left;margin-bottom:2px"  class="text">
  
     <Modal ref="ytmodal" @onConfirm="addCommand" />
      <editor-menu-bubble class="menububble" :editor="editor" @hide="hideLinkMenu" v-slot="{ commands, isActive, getMarkAttrs, menu }">
        <div
            class="menububble"
            :class="{ 'is-active': menu.isActive }"
            :style="`left: ${menu.left}px; bottom: ${menu.bottom}px;`"
        >
     
        <form class="menububble__form" v-if="linkMenuIsActive" @submit.prevent="setLinkUrl(commands.link, linkUrl)">
          <input class="menububble__input" type="text" v-model="linkUrl" placeholder="https://" ref="linkInput" @keydown.esc="hideLinkMenu"/>
          <button class="menububble__button" @click="setLinkUrl(commands.link, null)" type="button">
            <icon name="remove" />
          </button>
        </form>

        <template v-else>
          <button
            class="menububble__button"
            @click="showLinkMenu(getMarkAttrs('link'))"
            :class="{ 'is-active': isActive.link() }"
          >
            <span>{{ isActive.link() ? 'Update Link' : 'Add Link'}}</span>
            <icon name="link" />
          </button>
        </template>

      </div>
    </editor-menu-bubble>

    <editor-menu-bar class="editor-menu-bar" @hide="hideLinkMenu"  :editor="editor" v-slot="{ commands, isActive, }">
    <div>
      <button :class="{ 'is-active': isActive.bold() }" @click="commands.bold">
        B
      </button>
      <button :class="{ 'is-active': isActive.bold() }" @click="commands.underline">
        U
      </button>
      <button :class="{ 'is-active': isActive.heading({ level: 2 }) }" @click="commands.heading({ level: 2 })">
        H2
      </button>
      <button
          class="menubar__button"
          @click="showImagePrompt(commands.image)"
      >
          <!-- <icon name="image" /> -->
                   <svg viewBox="35.4 35.4 195.8 141.8" width="14" height="14">
  <path d="M70.9 35.4v17.8h17.7V35.4H70.9zm17.7 17.8v17.7H70.9v17.7H53.2V53.2H35.4V124h17.8v17.7h17.7v17.7h17.7v-17.7h88.6v17.7h17.7v-17.7h17.7V124h17.7V53.2h-17.7v35.4h-17.7V70.9h-17.7V53.2h-17.8v17.7H106.3V53.2H88.6zm88.6 0h17.7V35.4h-17.7v17.8zm17.7 106.2v17.8h17.7v-17.8h-17.7zm-124 0H53.2v17.8h17.7v-17.8zm17.7-70.8h17.7v17.7H88.6V88.6zm70.8 0h17.8v17.7h-17.8V88.6z"/>
          </svg>
      </button>
     <button class="menubar__button" @click="openModal(commands.iframe);">
          <svg viewBox="0 0 511.6 511.6" width="14" height="14">
            <path
              d="M511.3 213c-.1-10.3-1-23.3-2.4-39a354.4 354.4 0 0 0-6.1-42.1 66.4 66.4 0 0 0-19.9-35.1c-10.1-9.5-22-15-35.5-16.6-42.3-4.7-106.1-7.1-191.6-7.1-85.4 0-149.3 2.4-191.6 7.1a60.2 60.2 0 0 0-35.4 16.6 66.8 66.8 0 0 0-19.7 35.1A316 316 0 0 0 2.7 174a560.2 560.2 0 0 0-2.4 39 2430.9 2430.9 0 0 0 0 85.6 560 560 0 0 0 2.4 39c1.4 15.7 3.5 29.8 6.2 42.1a65.4 65.4 0 0 0 55.3 51.7c42.3 4.8 106.1 7.1 191.6 7.1s149.3-2.3 191.6-7.1c13.5-1.5 25.3-7 35.4-16.6a66.8 66.8 0 0 0 19.7-35c2.8-12.4 5-26.5 6.4-42.2 1.4-15.7 2.2-28.7 2.4-39a2450.8 2450.8 0 0 0 0-85.6zM357 271.2l-146.2 91.4a16.3 16.3 0 0 1-9.7 2.8c-2.9 0-5.8-.7-8.9-2.2a17 17 0 0 1-9.4-16V164.5a17 17 0 0 1 9.4-16 17.2 17.2 0 0 1 18.6.5L357 240.4c5.7 3.2 8.5 8.4 8.5 15.4s-2.8 12.2-8.5 15.4z"
            />
          </svg>
          
    </button>
    </div>
  </editor-menu-bar>
    <editor-content class="editor-content" style="font-size:20px" dir="rtl" :editor="editor" />   
    <input id="publish" type="submit" value="Publish">
      <pre><code>{{ html }} </code></pre>
    </form>
  </div>
</template>

<script>
import { Editor, EditorContent, EditorMenuBar,EditorMenuBubble  } from 'tiptap'
import Iframe from '../Iframe.js'
import Modal from "../Modal";
import {
  Blockquote,
  CodeBlock,
  HardBreak,
  Heading,
  Image,
  TrailingNode,
  OrderedList,
  BulletList,
  ListItem,
  TodoItem,
  TodoList,
  Bold,
  Code,
  Italic,
  Link,
  Strike,
  Underline,
  History,
} from 'tiptap-extensions'

export default {
  components: {
    EditorMenuBar,
    EditorContent,
    EditorMenuBubble,
    Modal
  },
  data() {
    return {
      editor: new Editor({
        extensions: [
          new Blockquote(),
          new CodeBlock(),
          new HardBreak(),
          new Heading({ levels: [1, 2, 3] }),
           new Image(),
           new TrailingNode(),        
          new Iframe(),
          new BulletList(),
          new OrderedList(),
          new ListItem(),
          new TodoItem(),
          new TodoList(),
          new Bold(),
          new Code(),
          new Italic(),
          new Link(),
          new Strike(),
          new Underline(),
          new History(),
        ],
        content: `
          <h1>Yay Headlines!</h1>
          <p>All these <strong>cool tags</strong> are working now.</p>
                <p>
            This is an example of a custom iframe node. This iframe is rendered as a <strong>vue component</strong>. This makes it possible to render the input below to change its source.
          </p>
          <iframe src="https://www.youtube.com/embed/XIMLoLxmTDw" frameborder="0" allowfullscreen></iframe>
        `,
        onUpdate: ({ getJSON, getHTML }) => {
          this.json = getJSON()
          this.html = getHTML()
        },
      }),
      linkUrl: null,
      linkMenuIsActive: false,
      html: 'Update content to see changes',
      title: 'title',
      type: 'sports',     
    }
  },  mounted() {
    this.setContent();
  },
  beforeDestroy() {
    this.editor.destroy()
  },
  computed:{
           slug: function() {
      var slug = this.sanitizeTitle(this.title);
      return slug;
    },excert: function(){
         let temp = document.createElement("div");
          temp.innerHTML = this.html;
         let sanitized = temp.textContent || temp.innerText;
         return sanitized
    }
  },
  
  methods: {
      sanitizeTitle: function(title) {
      var slug = "";
      // Change to lower case
      var titleLower = title.toLowerCase();
      // Letter "e"
      slug = titleLower.replace(/e|é|è|ẽ|ẻ|ẹ|ê|ế|ề|ễ|ể|ệ/gi, 'e');
      // Letter "a"
      slug = slug.replace(/a|á|à|ã|ả|ạ|ă|ắ|ằ|ẵ|ẳ|ặ|â|ấ|ầ|ẫ|ẩ|ậ/gi, 'a');
      // Letter "o"
      slug = slug.replace(/o|ó|ò|õ|ỏ|ọ|ô|ố|ồ|ỗ|ổ|ộ|ơ|ớ|ờ|ỡ|ở|ợ/gi, 'o');
      // Letter "u"
      slug = slug.replace(/u|ú|ù|ũ|ủ|ụ|ư|ứ|ừ|ữ|ử|ự/gi, 'u');
      // Letter "d"
      slug = slug.replace(/đ/gi, 'd');
      // Trim the last whitespace
      slug = slug.replace(/\s*$/g, '');
      // Change whitespace to "-"
      slug = slug.replace(/\s+/g, '-');
      
      return slug;
    },
    openModal(command) {
      this.$refs.ytmodal.showModal(command);
    },
    showImagePrompt(command) {
      const src = prompt('Enter the url of your image here')
      if (src !== null) {
        command({ src })
      }
    },
    addCommand(data) {
      if (data.command !== null) {
        data.command(data.data);
      }
    },
    setContent() {
      this.editor.setContent(this.content);
    },
    showLinkMenu(attrs) {
      this.linkUrl = attrs.href
      this.linkMenuIsActive = true
      this.$nextTick(() => {
        this.$refs.linkInput.focus()
      })
    },
    hideLinkMenu() {
      this.linkUrl = null
      this.linkMenuIsActive = false
    },
    setLinkUrl(command, url) {
      command({ href: url })
      this.hideLinkMenu()
    },
  }
}
</script>

<style scoped>

.editor-content  {
    width: 800px;
    margin: 0 auto;
    text-align: right
}
.editor-content iframe {
    width:100%!important;
    height: 25rem;
    border:0;
}
.editor-content img{
    width:100%!important
}
.menubar {
  display: flex;
  align-items: center;

  border-top: 1px solid #ddd;

  background-color: #e2e2e2;
}

.menubar__button {
  font-weight: 700;
  display: inline-block;
  width: 22px;
  height: 22px;
  background: rgba(0, 0, 0, 0);
  border: 0;
  padding: 0;
  color: #000;
  cursor: pointer;
  border-right: 1px solid #222;
}

path {
  fill: #cc181e;
}

.is-active {
  color: dodgerBlue;
}

.editor__content /deep/ .ProseMirror {
  text-align: left;
  background-color: rgb(242, 242, 242);
  border: 1px solid rgb(221, 221, 221);
  padding: 0.5rem;
}
.editor-content{
    background-color:#f0f0f0;
    min-height:200px    
}
.editor-menu-bar {
    margin:auto;
    width:800px;
    background-color:#e2e2e2;
    text-align: left
}
.text{
    padding:5px;
    font-size:18px
}
#publish{
    width:100%;
    padding:10px;
    color:white;
    font-weight:bold;
    background-color:#41B883
}
</style>
