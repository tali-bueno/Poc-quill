<template>
  <link href="https://cdn.jsdelivr.net/npm/quill@2.0.0/dist/quill.snow.css" rel="stylesheet" />
  <link rel="stylesheet" href="https://fonts.googleapis.com/css" />

  <div>
    <div id="editor" class="editor" >
  </div>
<div>
  {{ content }}
</div>
  </div>
</template>

<script setup>
import Quill from 'quill';
import QuillBetterTable from 'quill-better-table'
import BlotFormatter from 'quill-blot-formatter'
import "quill/dist/quill.core.css";
import 'quill-better-table/dist/quill-better-table.css'

const icons = Quill.import("ui/icons");
    icons["undo"] = `<svg viewbox="0 0 18 18">
    <polygon class="ql-fill ql-stroke" points="6 10 4 12 2 10 6 10"></polygon>
    <path class="ql-stroke" d="M8.09,13.91A4.6,4.6,0,0,0,9,14,5,5,0,1,0,4,9"></path>
  </svg>`;
    icons["redo"] = `<svg viewbox="0 0 18 18">
    <polygon class="ql-fill ql-stroke" points="12 10 14 12 16 10 12 10"></polygon>
    <path class="ql-stroke" d="M9.91,13.91A4.6,4.6,0,0,1,9,14a5,5,0,1,1,5-5"></path>
  </svg>`;

 const quillFonts  = ['arial',  'calibri',  'courier', 'georgia', 'lucida', 
                     'open', 'roboto' , 'verdana', 'trebuchet', 'tahoma' ]
const fonts = Quill.import('formats/font')
fonts.whitelist = quillFonts

Quill.register(DividerBlot);

Quill.register('modules/blotFormatter', BlotFormatter);
Quill.register({
  'modules/better-table': QuillBetterTable
}, true)

const toolbarOptions = [
  ['bold', 'italic', 'underline', 'strike'],        
  ['blockquote', 'code-block'],
  ['link', 'image'],

  [{ 'header': 1 }, { 'header': 2 }],               
  [{ 'list': 'ordered'}, { 'list': 'bullet' }, { 'list': 'check' }],
  [{ 'script': 'sub'}, { 'script': 'super' }],      
  [{ 'indent': '-1'}, { 'indent': '+1' }],          
  [{ 'direction': 'rtl' }],                         

  [{ 'size': ['small', false, 'large', 'huge'] }],  
  [{ 'header': [1, 2, 3, 4, 5, 6, false] }],

  [{ 'color': [] }, { 'background': [] }],          
  [{ font: fonts.whitelist }],
  [{ 'align': [] }],

  ['clean'], [{'table': 'TD'}],
  ['undo','redo']                                      
]

let quill = undefined
const content = ref('')

onMounted(() => {
  const options = {
  // debug: 'info',
  modules: {
    toolbar: toolbarOptions,
    history: {
      delay: 100,
      maxStack: 100,
      userOnly: true
    },
    table: false,
    "better-table": {
      operationMenu: {
        items: {
          unmergeCells: {
            text: "Another unmerge cells name"
          }
        },
        color: {
          colors: ["#fff", "red", "rgb(0, 0, 0)"],
          text: "Background Colors"
        }
      }
    },
    keyboard: {
      bindings: QuillBetterTable.keyboardBindings
    },
    blotFormatter: {
      overlay: {
        style: {
          border: '2px solid gray',
        }
      }
    },
    
  },
  placeholder: 'Compose an epic...',
  theme: 'snow'
};
const container = document.getElementsByClassName('editor');
quill = new Quill(container.editor, options)

const selectLocalImage = () =>{
  var input = document.createElement("input");
    input.setAttribute("type", "file");
    input.click();

    input.onchange = () => {
        let reader = new FileReader()
        const file = input.files[0];
        let base64 = ''

        reader.readAsDataURL(file)
        reader.onload = (() => {
          base64 = reader.result
          console.log('base', base64)

          if (/^image\//.test(file.type)) {
            console.log('reader',base64)
            insertToEditor(base64);
          } 
        })
    
    }
  
    }

const insertToEditor = (url) => {
    const range = quill.getSelection();
    // console.log('selction', range, url)
    quill.insertEmbed(range.index, "image", url);
}

quill.getModule('toolbar').addHandler('image', () => {
  selectLocalImage()
})

// quill.getModule('toolbar').addHandler('undo', () => {
//   console.log('undo', quill.history)
//   quill.history.undo()
// })

const customButtonUndo = document.querySelector('.ql-undo');
customButtonUndo.addEventListener('click', function () {
    quill.history.undo()
  });

const customButtonRedo = document.querySelector('.ql-redo');
customButtonRedo.addEventListener('click', function () {
    quill.history.redo()
  });

// quill.getModule('toolbar').addHandler('redo', () => {
//   console.log('redo', quill.history)
//   quill.history.redo()
// })

quill.getModule("toolbar").addHandler("table", () => {
  const tableModule = quill.getModule("better-table");
  tableModule.insertTable(3, 3);
});

quill.on('text-change', () => {
  const delta = quill.getSemanticHTML();
  content.value = delta
  // console.log('delatattaat', delta)
})

//Desabilitando a edição 
// quill.enable(false)

//Inserindo HTML no editor4
//  const value = '<p></p><div class="quill-better-table-wrapper"><table class="quill-better-table"><colgroup><col width="100"><col width="100"><col width="100"></colgroup><tbody><tr data-row="row-1mrr"><td data-row="row-1mrr" rowspan="1" colspan="1"><p class="qlbt-cell-line" data-row="row-1mrr" data-cell="cell-w7bd" data-rowspan="1" data-colspan="1"></p></td><td data-row="row-1mrr" rowspan="1" colspan="1"><p class="qlbt-cell-line" data-row="row-1mrr" data-cell="cell-sjdv" data-rowspan="1" data-colspan="1"></p></td><td data-row="row-1mrr" rowspan="1" colspan="1"><p class="qlbt-cell-line" data-row="row-1mrr" data-cell="cell-3z8q" data-rowspan="1" data-colspan="1"></p></td></tr><tr data-row="row-02a5"><td data-row="row-02a5" rowspan="1" colspan="1"><p class="qlbt-cell-line" data-row="row-02a5" data-cell="cell-4zt8" data-rowspan="1" data-colspan="1"></p></td><td data-row="row-02a5" rowspan="1" colspan="1"><p class="qlbt-cell-line" data-row="row-02a5" data-cell="cell-2iym" data-rowspan="1" data-colspan="1"></p></td><td data-row="row-02a5" rowspan="1" colspan="1"><p class="qlbt-cell-line" data-row="row-02a5" data-cell="cell-dil5" data-rowspan="1" data-colspan="1"></p></td></tr><tr data-row="row-pm2t"><td data-row="row-pm2t" rowspan="1" colspan="1"><p class="qlbt-cell-line" data-row="row-pm2t" data-cell="cell-xkwj" data-rowspan="1" data-colspan="1"></p></td><td data-row="row-pm2t" rowspan="1" colspan="1"><p class="qlbt-cell-line" data-row="row-pm2t" data-cell="cell-luto" data-rowspan="1" data-colspan="1"></p></td><td data-row="row-pm2t" rowspan="1" colspan="1"><p class="qlbt-cell-line" data-row="row-pm2t" data-cell="cell-40bp" data-rowspan="1" data-colspan="1"></p></td></tr></tbody></table></div><p></p>'
//  const delta = quill.clipboard.dangerouslyPasteHTML(value)

})

</script>

<style>

#editor {
  font-family: 'Arial';
  font-size: 18px;
  height: 375px;
} 


.ql-font-arial                {  font-family: 'Arial'             , sans-serif;}
.ql-font-roboto               {  font-family: 'Roboto'            , sans-serif;}
.ql-font-calibri              {  font-family: 'Calibri'           , sans-serif;}
.ql-font-courier              {  font-family: 'Courier New'       , serif;    }
.ql-font-georgia              {  font-family: 'Georgia'           , sans-serif;}
.ql-font-lucida               {  font-family: 'Lucida Sans Unicode', sans-serif;}
.ql-font-open                 {  font-family: 'Open Sans'         , sans-serif;}
.ql-font-tahoma               {  font-family: 'Tahoma'            , sans-serif;}
.ql-font-times                {  font-family: 'Times New Roman'   , sans-serif;}
.ql-font-trebuchet            {  font-family: 'Trebuchet Ms'      , sans-serif;}
.ql-font-verdana              {  font-family: 'Verdana'           , sans-serif;}

.ql-snow .ql-picker.ql-font .ql-picker-label[data-value=arial]::before,
.ql-snow .ql-picker.ql-font .ql-picker-item[data-value=arial]::before {
    content: 'Arial';  font-family: 'Arial', 'Helvetica';
}

.ql-snow .ql-picker.ql-font .ql-picker-label[data-value=calibri]::before,
.ql-snow .ql-picker.ql-font .ql-picker-item[data-value=calibri]::before {
    content: 'Calibri'; font-family: 'Calibri', sans-serif;
}

.ql-snow .ql-picker.ql-font .ql-picker-label[data-value=roboto]::before,
.ql-snow .ql-picker.ql-font .ql-picker-item[data-value=roboto]::before {
    content: 'Roboto'; font-family: 'Roboto', sans-serif;
}

.ql-snow .ql-picker.ql-font .ql-picker-label[data-value="courier"]::before,
.ql-snow .ql-picker.ql-font .ql-picker-item[data-value="courier"]::before {
    content: 'Courier New';  font-family: 'Courier New';
}

.ql-snow .ql-picker.ql-font .ql-picker-label[data-value=georgia]::before,
.ql-snow .ql-picker.ql-font .ql-picker-item[data-value=georgia]::before {
    content: 'Georgia';  font-family: 'Georgia', sans-serif;
}

.ql-snow .ql-picker.ql-font .ql-picker-label[data-value="lucida"]::before,
.ql-snow .ql-picker.ql-font .ql-picker-item[data-value="lucida"]::before {
    content: 'Lucida Sans';  font-family: 'Lucida Sans Unicode', sans-serif;
}

.ql-snow .ql-picker.ql-font .ql-picker-label[data-value="open"]::before,
.ql-snow .ql-picker.ql-font .ql-picker-item[data-value="open"]::before {
    content: 'Open Sans';  font-family: 'Open Sans', sans-serif;
}

.ql-snow .ql-picker.ql-font .ql-picker-label[data-value=tahoma]::before,
.ql-snow .ql-picker.ql-font .ql-picker-item[data-value=tahoma]::before {
    content: 'Tahoma';  font-family: 'Tahoma';
}

.ql-snow .ql-picker.ql-font .ql-picker-label[data-value="times"]::before,
.ql-snow .ql-picker.ql-font .ql-picker-item[data-value="times"]::before {
    content: 'Times New Roman';  font-family: 'Times New Roman';
}

.ql-snow .ql-picker.ql-font .ql-picker-label[data-value="trebuchet"]::before,
.ql-snow .ql-picker.ql-font .ql-picker-item[data-value="trebuchet"]::before {
    content: 'Trebuchet MS';  font-family: 'Trebuchet MS';
}
.ql-snow .ql-picker.ql-font .ql-picker-label[data-value=verdana]::before,
.ql-snow .ql-picker.ql-font .ql-picker-item[data-value=verdana]::before {
    content: 'Verdana';  font-family: 'Verdana', sans-serif;}
</style>
