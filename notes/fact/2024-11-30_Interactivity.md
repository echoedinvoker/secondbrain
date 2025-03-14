---
date: 2024-11-30
type: fact
aliases:
  -
hubs:
  - "[[Tailwindcss]]"
---

# Interactivity

```html
<!DOCTYPE html>
<html lang="en" class="scroll-smooth">
                  <!-- ^^^^^^^^^^^^^ when we click the anchor to scroll to certain section of the page, it will scroll smoothly -->
                  <!--               this class must be added to the html tag -->
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <script src="https://cdn.tailwindcss.com"></script>
    <title>Interactivity</title>
  </head>
  <body>
    <a href="#item">Scroll To Item</a>
    <!-- Hover State Styling -->
    <button
      type="button"
      class="bg-black text-white py-3 px-5 rounded-lg m-3 hover:bg-orange-500 hover:text-black"
    >
      Submit
    </button>
    <!-- Focus State Styling -->
    <button
      type="button"
      class="bg-black text-white py-3 px-5 rounded-lg m-3 focus:bg-green-500 focus:text-black"
    >
      Submit
    </button>
    <!-- Active State Styling -->
    <button
      type="button"
      class="bg-black text-white py-3 px-5 rounded-lg m-3 active:bg-yellow-500 active:text-black"
    >
      Submit
    </button>

    <br>

    <!-- Styling based on parent state -->
    <!-- When you need to style an element based on the state of some parent element, mark the parent with the group class, and use group-* modifiers like group-hover to style the target element: -->
    <a href="#" class="group block max-w-xs mx-auto rounded-lg p-6 bg-white shadow-lg space-y-3 hover:bg-sky-500">
                  <!-- ^^^^^ then we can use group-hover or other group-* modifiers to style the child element -->
      <div class="flex items-center">
        <h3 class="group-hover:text-white">Card Title</h3>
              <!-- ^^^^^^^^^^^^ this will change the color of this tag when the parent is hovered, -->
              <!--              if we use regular hover:text-white, it will change the color of this tag only when this tag itself is hovered -->
      </div>
      <p class="group-hover:text-white">This is some card text</p>
           <!-- ^^^^^^^^^^^^ -->
    </a>

 <br>

    <!-- Pseudo Classes -->
    <ul>
      <li class="even:bg-green-200 odd:bg-blue-200">Item 1</li>
            <!-- ^^^^^             ^^^^ in some frameworks, we write the html elements dynamically, so we can't add class to each element manually -->
            <!--                        but we can use pseudo classes to style the elements based on their position -->
      <li class="even:bg-green-200 odd:bg-blue-200">Item 2</li>
      <li class="even:bg-green-200 odd:bg-blue-200">Item 3</li>
      <li class="even:bg-green-200 odd:bg-blue-200">Item 4</li>
      <li class="even:bg-green-200 odd:bg-blue-200">Item 5</li>
      <li class="even:bg-green-200 odd:bg-blue-200">Item 6</li>
      <li class="even:bg-green-200 odd:bg-blue-200">Item 7</li>
      <li class="even:bg-green-200 odd:bg-blue-200">Item 8</li>
      <li class="even:bg-green-200 odd:bg-blue-200">Item 9</li>
      <li class="even:bg-green-200 odd:bg-blue-200">Item 10</li>
    </ul>

    <br />

    <!-- Appearance -->
    <!-- Use appearance-none to reset any browser specific styling on an element. This utility is often used when creating custom form components. -->
    <select>
      <option>Yes</option>
      <option>No</option>
    </select>

    <select class="appearance-none">
              <!-- ^^^^^^^^^^^^^^^ remove browser specific styling -->
      <option>Yes</option>
      <option>No</option>
    </select>

    <br />

    <!-- Cursor -->
    <button
      type="button"
      class="bg-black text-white py-3 px-5 rounded-lg m-3 cursor-pointer"
    >
      Submit
    </button>
    <button
      type="button"
      class="bg-black text-yellow-500 py-3 px-5 rounded-lg m-3 cursor-progress"
    >
      Loading...
    </button>
    <button
      type="button"
      disabled
      class="bg-black text-red-200 py-3 px-5 rounded-lg m-3 cursor-not-allowed"
    >
      Confirm
    </button>

    <br />

    <!-- Resize -->
    <textarea class="border border-black rounded resize"></textarea>

    <!-- User Select -->
    <div class="select-none">Lorem ipsum dolor sit amet.</div>
           <!-- ^^^^^^^^^^^ let user has no ability to select the text of this element -->
    <div class="select-text">Lorem ipsum dolor sit amet.</div>
    <div class="select-all">Lorem ipsum dolor sit amet.</div>
           <!-- ^^^^^^^^^^ let user always select all the text of this element, when they click on it -->
    <div class="select-auto">Lorem ipsum dolor sit amet.</div>

    <!-- below is a long text to test the scroll behavior, which is set to smooth in html tag -->
    <p>
      Lorem ipsum dolor sit amet consectetur adipisicing elit. Esse id eveniet
      quidem explicabo et molestiae rerum libero rem aperiam ad in fugiat, atque
      excepturi vel enim, suscipit iure inventore natus provident deserunt?
      Dolorum nihil fugiat sapiente odio nulla reiciendis qui repudiandae
      doloremque eos, voluptatem iste tenetur ipsam doloribus aperiam deleniti
      laboriosam consectetur in debitis quae a similique tempora eum ratione?
      Consequatur possimus dolor quidem! Iure, laborum doloribus. Recusandae
      odio, doloremque dolorum inventore, quaerat libero, quasi ad sunt aliquam
      dolor nihil possimus minima debitis ab perferendis in culpa nesciunt
      repellat consequuntur provident excepturi. Quod illo sequi sint, neque
      porro animi, delectus non sapiente amet fugit ea, dolor debitis ipsum ut
      temporibus officia nisi? Voluptas ex reprehenderit, itaque fugiat cumque
      quasi vero, non excepturi exercitationem dignissimos qui quis nemo quia
      delectus mollitia assumenda corrupti, ut incidunt quas ducimus aperiam
      explicabo minus? Corporis, modi. Unde eos laudantium delectus amet enim
      blanditiis culpa quasi quaerat, temporibus labore? Unde reprehenderit quod
      officiis odit animi voluptatibus deleniti aspernatur inventore quam ex
      saepe ea, laborum aliquam blanditiis odio corrupti aperiam molestiae id
      placeat qui! Perferendis nisi nesciunt assumenda, quos omnis error itaque
      laudantium inventore quasi ipsa laboriosam praesentium, minus modi soluta
      asperiores illum molestiae alias aliquid quia incidunt accusamus eaque
      quae. Veritatis ea provident aperiam architecto! Impedit sunt blanditiis,
      molestiae sequi reprehenderit in sapiente quos id! Maiores, similique a
      harum alias commodi, ullam esse nesciunt aliquam assumenda voluptatibus
      dolores, aperiam minus ut. Doloribus ipsam at ex quasi mollitia unde totam
      perferendis nobis obcaecati aperiam doloremque dignissimos dolores harum
      nam debitis officia, qui cum minus commodi corrupti odit, excepturi esse
      quibusdam numquam? Quos facilis eius eos error exercitationem. Cum, illo
      dolore. Accusamus voluptatibus odio vel repellendus eum iste corrupti
      voluptatem reiciendis molestias dolore, id quis expedita ipsam vero totam
      fugiat a minus eaque deserunt aspernatur vitae. Ea eveniet quae
      perspiciatis aliquid expedita optio voluptates reiciendis, molestias cum,
      obcaecati pariatur officiis blanditiis dignissimos nesciunt! Cum atque
      alias doloribus nam libero error quibusdam, minus nisi, enim accusantium
      est unde dolor distinctio esse labore? Iure expedita sed consequatur
      dolorem totam adipisci modi asperiores architecto dolorum mollitia
      excepturi numquam id quibusdam vitae itaque distinctio dolore labore et
      doloremque cumque sint, soluta ullam. Soluta beatae pariatur quas esse
      iure et nulla, perspiciatis, obcaecati sunt temporibus atque est
      asperiores, laboriosam odio rem veniam delectus corrupti fugiat fugit
      dicta? Eveniet modi sit facilis inventore voluptate et laborum
      consequuntur ad ex, eius possimus vero delectus vitae accusamus recusandae
      voluptatem cumque iusto at eligendi ut repellat deleniti? Neque ducimus
      numquam accusamus dicta laboriosam vel quis debitis molestias iure,
      mollitia facere natus blanditiis officia deserunt amet provident ratione
      odio sapiente cum sint. Impedit, voluptate distinctio quaerat ratione
      asperiores voluptatem dignissimos labore maiores saepe ex cum facere
      numquam quos reiciendis iste rerum quae blanditiis voluptates consequatur,
      nemo mollitia ipsum hic, esse suscipit? Dolor officia, ducimus
      reprehenderit iste voluptate qui minima odit a unde quo amet distinctio
      expedita. Voluptates optio fugiat vel eos quae fuga corporis explicabo
      magnam voluptatum repudiandae nesciunt odit, minus soluta neque!
      Distinctio est sunt eligendi blanditiis, sit ut ratione, et consequatur
      iusto atque sint neque sed, tenetur ullam quo recusandae veniam quisquam
      hic nostrum deleniti architecto! Voluptate voluptates sint velit, magnam
      in distinctio. Harum eaque vero voluptate velit suscipit commodi quae est
      neque expedita iste aspernatur porro modi, animi quo, quisquam, placeat
      praesentium rem omnis? Nobis, quia dolores, cupiditate hic quibusdam aut
      officiis maxime laborum tenetur illo quam reiciendis magni obcaecati
      dolorem explicabo nisi? Incidunt qui praesentium recusandae aliquam,
      asperiores suscipit deleniti magnam perferendis similique, voluptatum
      excepturi molestias maiores provident velit eius quis quisquam id iure.
      Delectus ab molestiae similique, minus vitae repellendus dolorem doloribus
      aliquam nemo suscipit dicta, at facere nihil. Expedita laborum, officiis
      neque accusantium laudantium rerum illum temporibus sequi ut modi, libero
      magni! Esse voluptatem asperiores excepturi fugiat provident doloribus
      neque consequuntur laboriosam amet voluptates? Veniam quaerat fugiat
      voluptas exercitationem adipisci. Voluptate, qui repellat totam tenetur a
      facere assumenda provident ipsum in quis eum atque ipsa quidem omnis,
      sunt, pariatur optio ullam porro sint illo? Quod debitis eligendi deleniti
      in ex suscipit optio animi dignissimos eaque quae, accusantium, nobis
      veniam velit officia. Praesentium sunt aut minus corrupti, qui tempora
      tenetur mollitia harum, voluptatum quibusdam obcaecati dolorum eum saepe
      beatae velit magnam, possimus quaerat ut amet. Illum consectetur, eum
      laboriosam assumenda, saepe iure aliquam nesciunt et cum, quis dolore?
      Totam numquam consequatur incidunt quod magni perferendis sit delectus
      tempora animi, ipsum facere natus corporis quam quaerat recusandae cumque,
      quasi sed fugit, aliquam necessitatibus nesciunt? Sequi reprehenderit
      molestias magnam fugiat ab perferendis iusto harum et odit sint illum
      molestiae amet quam quibusdam minima delectus ipsa consequatur,
      perspiciatis quod necessitatibus veritatis nesciunt, excepturi voluptates
      ea? Minima repellendus delectus earum fugit obcaecati error eveniet
      possimus, totam vel voluptates perspiciatis quaerat non modi? Vero culpa
      laborum, accusantium at consequatur explicabo, labore error debitis illum
      quibusdam totam mollitia, tempore recusandae sed maiores nihil ex qui
      dolorem? Facilis beatae fuga sunt aut nesciunt ipsum, similique sapiente,
      quia non voluptates laborum earum voluptate explicabo atque officiis
      pariatur consequuntur tenetur. Nobis doloribus odio harum laudantium sed
      exercitationem atque ratione, laboriosam soluta provident eum itaque, quae
      voluptas dicta obcaecati, nihil distinctio quos impedit delectus corrupti
      deserunt! Odit facilis laboriosam veniam reprehenderit eos, maiores animi
      obcaecati laudantium tempore? Ex ducimus eaque commodi molestiae debitis
      culpa repellat voluptatem ea deleniti nesciunt unde dolore, earum nihil
      cum quos laboriosam magni illo iure aperiam quidem itaque! Quod minus nemo
      recusandae mollitia ipsam qui delectus odio id voluptatem esse natus
      corrupti fugit obcaecati, voluptate quidem doloremque modi, optio, quam
      quaerat provident eveniet repellat laborum dolorum sequi. Quos mollitia
      et, voluptatum pariatur iste nisi velit voluptatem eos! Natus similique
      possimus ea vel! Magnam adipisci necessitatibus vitae amet iste. Modi
      omnis repellat molestiae, eum officia iusto perferendis quis numquam harum
      voluptatum ut iure totam neque assumenda provident? Impedit, officiis
      laboriosam! Recusandae quo sapiente porro vel minima eum. Commodi totam
      consequatur neque aperiam doloribus. Odit adipisci repellat ducimus,
      numquam incidunt iste repellendus officia iusto eligendi voluptatem
      pariatur, porro facilis reiciendis id tempore. Obcaecati eos assumenda
      animi et totam aliquam cupiditate ea.
    </p>

    <p>
      Lorem ipsum dolor sit amet consectetur, adipisicing elit. Consequatur
      adipisci mollitia odio aspernatur, expedita repellendus nisi aut, dolorem
      corporis ipsam dolor sed ducimus atque tenetur officiis saepe in quaerat
      enim doloremque alias iure! Nemo, quos veniam accusamus quae quisquam
      tempora fugit. Debitis repellendus totam ex? Et voluptatum sunt maxime
      cupiditate! Vero earum perferendis esse temporibus amet nesciunt
      distinctio aliquid voluptates sequi? Hic iste tempora accusamus,
      architecto animi reiciendis fugit rerum dolorum quisquam pariatur sint nam
      eligendi placeat magni harum non nihil quas voluptatibus. Exercitationem
      quae natus perspiciatis vitae, dolorum modi libero commodi quam
      voluptatibus minus eaque, numquam dolor itaque, fuga tempora nobis!
      Similique aut consequatur vitae, inventore repellendus ad possimus natus
      distinctio ratione nam consequuntur unde id ipsum, maxime eius dicta odit?
      Repellendus accusantium dolorem facilis distinctio quos? Cupiditate sed
      totam harum? Quaerat autem assumenda optio, doloremque aperiam molestias
      ullam eveniet, quod temporibus rerum eligendi iste distinctio fuga,
      praesentium asperiores consequuntur debitis hic! Corrupti sapiente ipsum
      quas libero! Error voluptates ad molestias dolorem doloremque omnis quasi
      asperiores delectus voluptatem ullam? Adipisci sequi natus distinctio,
      totam enim doloribus cupiditate eum delectus inventore obcaecati
      aspernatur quod itaque dolor. Nesciunt, soluta voluptatem optio suscipit
      consectetur ipsam enim voluptate corrupti quaerat. Quis minima itaque
      doloribus quod eveniet reprehenderit error facilis. Facere quos, eos
      reiciendis quae, perspiciatis in fugiat dolore recusandae laudantium,
      aliquid atque consequuntur? Laborum quo, perspiciatis odit excepturi
      ratione in quam iste consequuntur facere, sunt voluptas corrupti hic
      dolorum debitis! Voluptates aut rem fuga beatae explicabo velit maiores
      accusamus accusantium consequatur? Eveniet, blanditiis quam autem ea
      numquam accusamus. Odit quidem explicabo, maxime vero molestiae facere
      incidunt omnis. A id, voluptatem distinctio dolor veniam pariatur maxime
      eaque minima commodi nostrum! Nulla nisi voluptas rerum ducimus officia
      voluptatum repellat veniam velit non error mollitia facere tenetur, sequi
      ut illo totam eius omnis quam quisquam sit quia sunt ea? Molestias nobis
      rem molestiae odit veritatis illum, quidem nihil! Officiis eaque autem
      veniam magni magnam quisquam tempora quo quod culpa odit, velit error
      aliquid dolores aliquam et, ad nostrum neque exercitationem voluptate
      delectus, ipsa explicabo! Dignissimos explicabo consectetur voluptates
      necessitatibus tenetur vel unde fugiat laboriosam eum? Voluptates quisquam
      sequi amet minus nostrum assumenda. Omnis quos maxime, ipsam rerum tempora
      unde cupiditate deserunt veniam asperiores, saepe illum eveniet!
      Distinctio inventore quae officia aliquam tempora accusantium modi!
      Officiis facere deleniti corrupti doloremque nam asperiores excepturi sint
      voluptates. Officia ut autem ipsam? Ipsum fugit ea voluptates sed, officia
      expedita vel saepe assumenda ipsam minus tempore numquam aliquid impedit
      reprehenderit similique commodi illum perferendis ipsa reiciendis cumque.
      Ipsam explicabo assumenda consectetur, adipisci perspiciatis obcaecati non
      provident iusto praesentium debitis? Eligendi cum natus hic optio
      corporis, distinctio aliquid iusto ratione iste neque sint totam magni
      eveniet adipisci quibusdam, atque aspernatur rem? Itaque inventore
      expedita aut nemo aperiam delectus quisquam rem tenetur mollitia
      doloremque dolores ad neque molestiae necessitatibus sit, dignissimos
      ipsam at. Voluptas quis exercitationem nam non, voluptatem eos nulla iure
      consequatur, esse ut ipsa ab necessitatibus expedita, tempore odio
      voluptate deserunt ea corrupti nemo est consequuntur magni quaerat
      recusandae ipsam! Porro aperiam totam possimus, eius, libero iusto, quo ex
      quisquam voluptatum expedita voluptate error. Et facere cum ratione?
      Consequuntur aut recusandae et quod maxime deleniti autem at rem ex
      inventore saepe doloremque consequatur ratione, iusto amet fugiat
      repellendus praesentium nostrum. Repudiandae optio temporibus laboriosam
      autem. Magni, ab, neque sunt itaque quam eos accusantium exercitationem
      dolorum blanditiis dignissimos iure! Autem perspiciatis ad quidem rem
      deserunt numquam consequatur omnis in, eligendi aperiam magni odio modi
      sequi, laborum eos cupiditate velit beatae deleniti dolor cum recusandae
      ipsam voluptas quam asperiores. Dolorum officiis iste maiores quae, iusto
      laboriosam quis neque nemo atque perspiciatis explicabo quasi distinctio
      odio at eos corrupti aliquid fuga omnis voluptatibus quidem, reprehenderit
      consequatur ex culpa ab. Repellat, odit ipsum. Eos, fugit, magnam ea,
      itaque ipsum doloremque facere harum pariatur architecto quas fuga esse
      quos autem sunt dolorem? Minima vitae enim et quia quaerat ad esse
      inventore. Cumque earum laboriosam officiis, assumenda totam culpa
      incidunt. Qui ex molestiae voluptates eius recusandae praesentium magnam
      esse dicta. Accusamus enim rerum tempore odit, quasi placeat! Voluptas
      consequatur dicta dolor veniam dignissimos modi molestiae quos labore
      tempora architecto! Veniam animi nostrum aliquam cum at dolorum eveniet
      tempore explicabo eligendi, modi enim quia earum quasi officiis. Quas,
      exercitationem laudantium fugit perferendis at veritatis sapiente itaque
      praesentium ullam ea voluptates numquam, quam velit officia sunt vel quia
      pariatur asperiores porro est explicabo? Libero deleniti nisi inventore.
      Quisquam aut saepe fugiat illum eius laboriosam magni illo blanditiis
      vitae explicabo quia quas sapiente quis beatae, iste vero quaerat
      perferendis atque nobis unde! Recusandae nihil aut officiis amet quos
      ducimus quo, porro unde exercitationem perspiciatis blanditiis dicta magni
      nemo illum voluptatibus placeat laboriosam, cum alias distinctio!
      Voluptatibus eaque recusandae ad modi voluptate odio praesentium eum rem
      quae quia. Repellendus voluptatum illum, asperiores debitis saepe ipsa
      dolorum? Non obcaecati ut qui quos voluptatum ducimus saepe. Velit dolor
      est dolores iure architecto alias explicabo quae, veniam incidunt eos,
      quod quidem recusandae saepe placeat enim! Maxime provident quos,
      reprehenderit aut molestias nemo nam nesciunt atque error in sapiente,
      necessitatibus quo inventore vitae, esse corrupti minus. Laboriosam non
      deleniti doloremque ullam veritatis quod reprehenderit veniam, ipsum unde,
      ea molestias beatae exercitationem aut incidunt. Sed consequuntur
      molestiae iste tempora voluptate veritatis tenetur cumque voluptates iure
      eum labore at, vitae magni delectus maiores eaque quasi voluptatibus
      praesentium perspiciatis amet minus illum. Corporis pariatur, voluptas
      illum cumque maxime consequuntur vero dolor veritatis dicta. Sed sunt
      laudantium aperiam error quidem obcaecati, quaerat totam repellendus ipsam
      voluptas aspernatur veniam, expedita deleniti repellat ea perferendis at
      doloribus commodi dolores dicta, sequi quos cupiditate? Fugit quibusdam
      neque pariatur! Nostrum vel deleniti est dolor sapiente reiciendis quo
      inventore unde consequatur esse non consectetur, facere ea laboriosam ex
      modi illo accusamus repellat labore sint ullam harum eligendi. Asperiores,
      porro eaque eum assumenda ratione culpa esse ab cupiditate dolores!
      Dolorem explicabo ea minima numquam cum et veniam quos accusamus quod
      laudantium animi, doloremque a cumque exercitationem, quis maiores facere!
      At odio amet saepe esse cupiditate. Veritatis, sequi! Ipsa sunt saepe
      eaque suscipit nisi voluptatem. Et.
    </p>

    <div id="item">Item</div>
  </body>
</html>

<!-- Cursor
  cursor-auto	          cursor: auto;
  cursor-default	      cursor: default;
  cursor-pointer	      cursor: pointer;
  cursor-wait	          cursor: wait;
  cursor-text	          cursor: text;
  cursor-move	          cursor: move;
  cursor-help	          cursor: help;
  cursor-not-allowed	  cursor: not-allowed;
  cursor-none	          cursor: none;
  cursor-context-menu	  cursor: context-menu;
  cursor-progress	      cursor: progress;
  cursor-cell	          cursor: cell;
  cursor-crosshair	    cursor: crosshair;
  cursor-vertical-text	cursor: vertical-text;
  cursor-alias	        cursor: alias;
  cursor-copy	          cursor: copy;
  cursor-no-drop	      cursor: no-drop;
  cursor-grab	          cursor: grab;
  cursor-grabbing	      cursor: grabbing;
  cursor-all-scroll	    cursor: all-scroll;
  cursor-col-resize	    cursor: col-resize;
  cursor-row-resize	    cursor: row-resize;
  cursor-n-resize	      cursor: n-resize;
  cursor-e-resize	      cursor: e-resize;
  cursor-s-resize	      cursor: s-resize;
  cursor-w-resize	      cursor: w-resize;
  cursor-ne-resize	    cursor: ne-resize;
  cursor-nw-resize	    cursor: nw-resize;
  cursor-se-resize	    cursor: se-resize;
  cursor-sw-resize	    cursor: sw-resize;
  cursor-ew-resize	    cursor: ew-resize;
  cursor-ns-resize	    cursor: ns-resize;
  cursor-nesw-resize	  cursor: nesw-resize;
  cursor-nwse-resize	  cursor: nwse-resize;
  cursor-zoom-in	      cursor: zoom-in;
  cursor-zoom-out	      cursor: zoom-out;
-->

```

