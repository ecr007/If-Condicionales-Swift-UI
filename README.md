# If-Condicionales-Swift-UI

<article>
<div class="content">
<a style="text-decoration: none" href="/articles/conditional-modifiers">
<h1 class=" notranslate">Conditional view modifiers</h1>
</a>
<div class="tag-list">
<a style="color: var(--tag-color); background-color: #474ae8;" href="/swiftui/"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">SwiftUI</font></font></a>
<div style="color: var(--secondary-text-color);"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">1 de septiembre de 2020</font></font></div>
</div><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Cuando trabajamos con vistas SwiftUI, a veces nos gustaría aplicar diferentes modificadores basados ​​en condiciones / estados:</font></font></p><pre class=" notranslate"><code class=" notranslate"><span class="keyword">var</span> body: <span class="keyword">some</span> view {
  myView
    <span class="comment">// if X
    // .padding(8)
    // if Y
    // .background(Color.blue)</span>
}
</code></pre><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">En muchos casos, podemos pasar un </font></font><em><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">argumento</font></font></em><font style="vertical-align: inherit;"><font style="vertical-align: inherit;"> modificador diferente </font><font style="vertical-align: inherit;">en función de la condición, y eso se encargará de ello:</font></font></p><pre class=" notranslate"><code class=" notranslate"><span class="keyword">var</span> body: <span class="keyword">some</span> view {
  myView
    .<span class="call">padding</span>(<span class="type">X</span> ? <span class="number">8</span> : <span class="number">0</span>)
    .<span class="call">background</span>(<span class="type">Y</span> ? <span class="type">Color</span>.<span class="property">blue</span> : <span class="type">Color</span>.<span class="property">clear</span>)
}
</code></pre><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Si bien esto funciona aquí, hay otros modificadores, </font></font><code class=" notranslate">.hidden()</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">por nombrar uno, donde esta solución no funciona: </font></font><br><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">en este artículo, exploremos cómo podemos encargarnos de tales casos.</font></font></p><h2 class=" notranslate">The if view extension</h2><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">La solución más común es definir una nueva </font></font><code class=" notranslate">if</code> <code class=" notranslate">View</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">extensión:</font></font></p><pre class=" notranslate"><code class=" notranslate"><span class="keyword">extension</span> <span class="type">View</span> {
  <span class="keyword">@ViewBuilder
  func</span> `if`&lt;<span class="type">Transform</span>: <span class="type">View</span>&gt;(
    <span class="keyword">_</span> condition: <span class="type">Bool</span>, 
    transform: (<span class="type">Self</span>) -&gt; <span class="type">Transform</span>
  ) -&gt; <span class="keyword">some</span> <span class="type">View</span> {
    <span class="keyword">if</span> condition {
      <span class="call">transform</span>(<span class="keyword">self</span>)
    } <span class="keyword">else</span> {
      <span class="keyword">self</span>
    }
  }
}
</code></pre><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Esta función se aplicará </font></font><code class=" notranslate">transform</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">a nuestra vista cuando </font></font><code class=" notranslate">condition</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">sea </font></font><code class=" notranslate">true</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">, de lo contrario dejará la vista original intacta.</font></font></p><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Volviendo a nuestro ejemplo, esta es una forma de usarlo:</font></font></p><pre class=" notranslate"><code class=" notranslate"><span class="keyword">var</span> body: <span class="keyword">some</span> view {
  myView
    .<span class="call">if</span>(<span class="type">X</span>) { $0.<span class="call">padding</span>(<span class="number">8</span>) }
    .<span class="call">if</span>(<span class="type">Y</span>) { $0.<span class="call">background</span>(<span class="type">Color</span>.<span class="property">blue</span>) }
}
</code></pre><h2 class=" notranslate">If else view extension</h2><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Dependiendo de cuán compactas queramos que sean nuestras declaraciones, aplicar diferentes modificadores basados ​​en el </font><font style="vertical-align: inherit;">valor </font></font><code class=" notranslate">condition</code> <code class=" notranslate">true</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">/ </font></font><code class=" notranslate">false</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">nos costaría al menos dos modificadores:</font></font></p><pre class=" notranslate"><code class=" notranslate"><span class="keyword">var</span> body: <span class="keyword">some</span> view {
  myView
    .<span class="call">if</span>(<span class="type">X</span>) { $0.<span class="call">padding</span>(<span class="number">8</span>) }
    .<span class="call">if</span>(!<span class="type">X</span>) { $0.<span class="call">background</span>(<span class="type">Color</span>.<span class="property">blue</span>) }
}
</code></pre><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Esto es claro y ya conciso, sin embargo, si realmente queremos ir all-in con </font></font><code class=" notranslate">View</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">extensiones, podemos definir una nueva </font></font><code class=" notranslate">if</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">sobrecarga que nos permita modificar la </font></font><code class=" notranslate">else</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">rama también:</font></font></p><pre class=" notranslate"><code class=" notranslate"><span class="keyword">extension</span> <span class="type">View</span> {
  <span class="keyword">@ViewBuilder
  func</span> `if`&lt;<span class="type">TrueContent</span>: <span class="type">View</span>, <span class="type">FalseContent</span>: <span class="type">View</span>&gt;(
    <span class="keyword">_</span> condition: <span class="type">Bool</span>, 
    if ifTransform: (<span class="type">Self</span>) -&gt; <span class="type">TrueContent</span>, 
    else elseTransform: (<span class="type">Self</span>) -&gt; <span class="type">FalseContent</span>
  ) -&gt; <span class="keyword">some</span> <span class="type">View</span> {
    <span class="keyword">if</span> condition {
      <span class="call">ifTransform</span>(<span class="keyword">self</span>)
    } <span class="keyword">else</span> {
      <span class="call">elseTransform</span>(<span class="keyword">self</span>)
    }
  }
}
</code></pre><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Lo que hará que nuestro ejemplo use un solo modificador:</font></font></p><pre class=" notranslate"><code class=" notranslate"><span class="keyword">var</span> body: <span class="keyword">some</span> view {
  myView
    .<span class="call">if</span>(<span class="type">X</span>) { $0.<span class="call">padding</span>(<span class="number">8</span>) } else: { $0.<span class="call">background</span>(<span class="type">Color</span>.<span class="property">blue</span>) }
}
</code></pre><h2 class=" notranslate">IfLet view extension</h2><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">De manera similar a las condiciones, a veces queremos aplicar un modificador solo cuando otro valor no lo es </font></font><code class=" notranslate">nil</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">, de manera similar a como </font></font><code class=" notranslate">if let</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">funciona </font><font style="vertical-align: inherit;">Swift </font><font style="vertical-align: inherit;">, y usar ese valor en el modificador mismo.</font></font></p><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">En este caso podemos definir una nueva </font></font><code class=" notranslate">View</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">extensión que nos permita hacer precisamente eso:</font></font></p><pre class=" notranslate"><code class=" notranslate"><span class="keyword">extension</span> <span class="type">View</span> {
  <span class="keyword">@ViewBuilder
  func</span> ifLet&lt;V, Transform: <span class="type">View</span>&gt;(
    <span class="keyword">_</span> value: <span class="type">V</span>?, 
    transform: (<span class="type">Self</span>, <span class="type">V</span>) -&gt; <span class="type">Transform</span>
  ) -&gt; <span class="keyword">some</span> <span class="type">View</span> {
    <span class="keyword">if let</span> value = value {
      <span class="call">transform</span>(<span class="keyword">self</span>, value)
    } <span class="keyword">else</span> {
      <span class="keyword">self</span>
    }
  }
}
</code></pre><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">La diferencia con antes es que esta nueva función:</font></font></p><ul><li><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">toma un genérico opcional en </font></font><code class=" notranslate">value</code> <code class=" notranslate">V</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">lugar de una </font></font><code class=" notranslate">Bool</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">condición</font></font></li><li><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">pasa este genérico </font></font><code class=" notranslate">value</code> <code class=" notranslate">V</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">como parámetro de la función de transformación</font></font></li></ul><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">A continuación, se muestra un ejemplo en el que a </font></font><code class=" notranslate">View</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">aplica un color de primer plano solo cuando </font></font><code class=" notranslate">optionalColor</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">se establece:</font></font></p><pre class=" notranslate"><code class=" notranslate"><span class="keyword">var</span> body: <span class="keyword">some</span> view {
  myView
    .<span class="call">ifLet</span>(optionalColor) { $0.<span class="call">foregroundColor</span>($1) }
}
</code></pre><h2 class=" notranslate">iOS Availability modifiers</h2><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Hace meses </font></font><a href="/articles/use-new-features-mantain-backward-compatibility/"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">cubrimos un enfoque</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;"> sobre cómo usar las nuevas funciones de iOS manteniendo la compatibilidad con versiones anteriores. </font><font style="vertical-align: inherit;">Ahora nos encontramos en una situación similar con SwiftUI, donde se han introducido nuevos modificadores y donde nos gustaría enviar una aplicación compatible con versiones anteriores de iOS 13.</font></font></p><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Desafortunadamente, en estas situaciones no podemos usar las extensiones genéricas que acabamos de presentar:</font></font></p><ul><li><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Swift </font></font><code class=" notranslate">#available</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">y </font></font><code class=" notranslate">@available</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">no se pueden pasar como argumentos en nuestro </font></font><code class=" notranslate">if</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">modificador</font></font></li><li><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">no podemos garantizarle al compilador que nuestra </font></font><code class=" notranslate">transform</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">función se aplicaría solo en iOS 14 / 13.4 y posterior</font></font></li></ul><blockquote><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Si encuentra una manera, ¡ </font></font><a href="https://twitter.com/zntfdr"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">me encantaría saberlo</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;"> !</font></font></p></blockquote><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Una forma de superar esto es definir un modificador de vista diferente para cada uno de nuestros casos de uso.</font></font></p><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Por ejemplo, aquí hay una </font></font><code class=" notranslate">View</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">extensión para ignorar el teclado en nuestro diseño:</font></font></p><pre class=" notranslate"><code class=" notranslate"><span class="keyword">extension</span> <span class="type">View</span> {
  <span class="keyword">@ViewBuilder
  func</span> ignoreKeyboard() -&gt; <span class="keyword">some</span> <span class="type">View</span> {
    <span class="keyword">if #available</span>(iOS <span class="number">14.0</span>, *) {
      <span class="call">ignoresSafeArea</span>(.<span class="dotAccess">keyboard</span>)
    } <span class="keyword">else</span> {
      <span class="keyword">self</span> <span class="comment">// iOS 13 always ignores the keyboard</span>
    }
  }
}
</code></pre><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Y aquí le mostramos cómo tener el </font></font><code class=" notranslate">InsetGroupedList</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">estilo tanto en iOS 13 como en iOS 14:</font></font></p><pre class=" notranslate"><code class=" notranslate"><span class="keyword">extension</span> <span class="type">List</span> {
  <span class="keyword">@ViewBuilder
  func</span> insetGroupedListStyle() -&gt; <span class="keyword">some</span> <span class="type">View</span> {
    <span class="keyword">if #available</span>(iOS <span class="number">14.0</span>, *) {
      <span class="keyword">self</span>
        .<span class="call">listStyle</span>(<span class="type">InsetGroupedListStyle</span>())
    } <span class="keyword">else</span> {
      <span class="keyword">self</span>
        .<span class="call">listStyle</span>(<span class="type">GroupedListStyle</span>())
        .<span class="call">environment</span>(\.<span class="property">horizontalSizeClass</span>, .<span class="dotAccess">regular</span>)
    }
  }
}
</code></pre><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Para ser utilizado de la siguiente manera:</font></font></p><pre class=" notranslate"><code class=" notranslate"><span class="keyword">var</span> body: <span class="keyword">some</span> view {
  myView
    .<span class="call">ignoreKeyboard</span>()
}

<span class="comment">// ...</span>

<span class="keyword">var</span> body: <span class="keyword">some</span> view {
  myView
    .<span class="call">insetGroupedListStyle</span>()
}
</code></pre><h3 class=" notranslate">Availability Attributes</h3><blockquote><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Créditos a </font></font><a href="https://twitter.com/olebegemann"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Ole Begemann</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;"> por </font></font><a href="https://twitter.com/olebegemann/status/1294583027583123458"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">este consejo</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;"> .</font></font></p></blockquote><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">En algún momento en el futuro, nuestras bases de código dejarán de ser compatibles con iOS 13, lo que hará que nuestras extensiones sean innecesarias: </font></font><br><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">¿no sería genial si Xcode pudiera avisarnos cuando esto suceda?</font></font></p><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">De manera similar a cómo las bibliotecas / marcos definen la disponibilidad de la API, también podemos usar el </font></font><code class=" notranslate">@availability</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">atributo en nuestras nuevas extensiones:</font></font></p><pre class=" notranslate"><code class=" notranslate"><span class="keyword">@available</span>(
  iOS, introduced: <span class="number">13</span>, deprecated: <span class="number">14</span>,
  message: <span class="string">"Use .ignoresSafeArea(.keyboard) directly"</span>
) 
<span class="keyword">extension</span> <span class="type">View</span> {
  <span class="keyword">@ViewBuilder
  func</span> ignoreKeyboard() -&gt; <span class="keyword">some</span> <span class="type">View</span> {
    ...
  }
}

<span class="comment">/// ...</span>

<span class="keyword">@available</span>(
  iOS, introduced: <span class="number">13</span>, deprecated: <span class="number">14</span>, 
  message: <span class="string">"Use .listStyle(InsetGroupedListStyle()) directly"</span>
)
<span class="keyword">extension</span> <span class="type">List</span> {
  <span class="keyword">@ViewBuilder
  func</span> insetGroupedListStyle() -&gt; <span class="keyword">some</span> <span class="type">View</span> {
    ...
  }
}
</code></pre><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Agregar este </font></font><code class=" notranslate">@available</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">atributo activará una advertencia de obsolescencia dondequiera que se utilicen estas funciones, solo después de que se elimine la compatibilidad con iOS 13: </font></font><br><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">también podemos agregar un mensaje opcional que nos recuerde qué hacer una vez que </font><font style="vertical-align: inherit;">se active </font><font style="vertical-align: inherit;">la advertencia.</font></font></p><h2 class=" notranslate">Conclusions</h2><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Las API declarativas de SwiftUI facilitan </font></font><code class=" notranslate">View</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">la definición de s: cuando nuestras vistas necesitan aplicar diferentes modificadores en función de ciertas condiciones, podemos definir nuestros propios modificadores de vista condicionales, lo que nos permite mantener la misma declaratividad a la que estamos acostumbrados.</font></font></p><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">¡Gracias por leer y </font></font><a href="/feed.rss"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">estar atento</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;"> a más artículos de SwiftUI!</font></font></p>
</div>
</article>
