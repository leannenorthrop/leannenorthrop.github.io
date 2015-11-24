---
layout: post
title: Starting a Play Console to Work with Slick 3.0
tags: [scala, play_2_4, slick_3_0]
blogpost: true
---

The Getting Started documentation for both Play 2.4 and Slick 3.0 unfortunately have a few errors in the code examples making it a bit of a nightmare for a Play/Slick newbie. After hunting around I've discovered to start play in a console the start code is now `play.core.server.ProdServerStart.main(Array())`. So to use Play 2.4 interactively:

<pre><code class="bash hljs">$ activator
[info] Loading project definition from ...
[info] Set current project to play-slick
[play-slick] $ console
[info] Starting scala interpreter...
[info] 
Welcome to Scala version <span class="hljs-number">2.11</span>.<span class="hljs-number">7</span> (Java HotSpot(TM) <span class="hljs-number">64</span>-Bit Server VM, Java <span class="hljs-number">1.8</span>.<span class="hljs-number">0</span>_51).
Type <span class="hljs-keyword">in</span> expressions to have them evaluated.
Type :<span class="hljs-built_in">help</span> <span class="hljs-keyword">for</span> more information.

scala&gt; play.core.server.ProdServerStart.main(Array())
[info] - play.api.libs.concurrent.ActorSystemProvider - Starting application default Akka system: application
[info] - play.api.Play - Application started (Prod)
[info] - play.core.server.NettyServer - Listening <span class="hljs-keyword">for</span> HTTP on /<span class="hljs-number">0</span>:<span class="hljs-number">0</span>:<span class="hljs-number">0</span>:<span class="hljs-number">0</span>:<span class="hljs-number">0</span>:<span class="hljs-number">0</span>:<span class="hljs-number">0</span>:<span class="hljs-number">0</span>:<span class="hljs-number">9000</span>

scala&gt;
</code></pre>

If your project is configured with Slick 3.0 to load up Slick driver use `:paste` mode:

<pre><code class="bash hljs">scala&gt; :paste
// Entering paste mode (ctrl-D to finish)

import play.api.db.slick.DatabaseConfigProvider
import slick.driver.JdbcProfile
import scala.language.existentials
val dbConfig = DatabaseConfigProvider.get[JdbcProfile](play.api.Play.current)
import dbConfig.driver.api._
val db = dbConfig.db
</code></pre>

Slick can now be used:

<pre><code class="bash hljs">scala&gt; :paste
// Entering paste mode (ctrl-D to finish)

 <span class="hljs-keyword">case</span> class AccumulationRow(
  id: Long,
  mantraId: Long,
  userId: String,
  gatheringId: Long,
  count: Long,
  year:Int,
  month:Int,
  day:Int)

  class AccumulationTable(tag: Tag) extends Table[AccumulationRow](tag, <span class="hljs-string">"accumulations"</span>) {
    def id = column[Long](<span class="hljs-string">"id"</span>, O.AutoInc, O.PrimaryKey)
    def mantraId = column[Long](<span class="hljs-string">"mantra_id"</span>)
    def userId = column[String](<span class="hljs-string">"user_id"</span>)
    def gatheringId = column[Long](<span class="hljs-string">"gathering_id"</span>)
    def count = column[Long](<span class="hljs-string">"count"</span>)
    def year = column[Int](<span class="hljs-string">"year"</span>)
    def month = column[Int](<span class="hljs-string">"month"</span>)
    def day = column[Int](<span class="hljs-string">"day"</span>)
    def * = (id, mantraId, userId, gatheringId, count, year, month, day) &lt;&gt; (AccumulationRow.tupled, AccumulationRow.unapply)
  }
  val slickAccumulations = TableQuery[AccumulationTable]
  import scala.concurrent.duration._
  import scala.concurrent._
  import models.daos._
  val q = slickAccumulations.length
  Await.result(db.run(q.result), Duration(<span class="hljs-number">1000</span>, MILLISECONDS))
</code></pre>