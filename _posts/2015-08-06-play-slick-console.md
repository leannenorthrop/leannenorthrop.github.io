---
layout: post
title: Starting a Play Console to Work with Slick 3.0
tags: [scala, play_2_4, slick_3_0]
blogpost: true
---

The Getting Started documentation for both Play 2.4 and Slick 3.0 unfortunately have a few errors in the code examples making it a bit of a nightmare for a Play/Slick newbie. After hunting around I've discovered to start play in a console the start code is now `play.core.server.ProdServerStart.main(Array())`. So to use Play 2.4 interactively:

<pre><code class="bash">$ activator
[info] Loading project definition from ...
[info] Set current project to play-slick
[play-slick] $ console
[info] Starting scala interpreter...
[info] 
Welcome to Scala version 2.11.7 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_51).
Type in expressions to have them evaluated.
Type :help for more information.

scala> play.core.server.ProdServerStart.main(Array())
[info] - play.api.libs.concurrent.ActorSystemProvider - Starting application default Akka system: application
[info] - play.api.Play - Application started (Prod)
[info] - play.core.server.NettyServer - Listening for HTTP on /0:0:0:0:0:0:0:0:9000

scala>
</code></pre>

If your project is configured with Slick 3.0 to load up Slick driver use `:paste` mode:

<pre><code class="bash">scala> :paste
// Entering paste mode (ctrl-D to finish)

import play.api.db.slick.DatabaseConfigProvider
import slick.driver.JdbcProfile
import scala.language.existentials
val dbConfig = DatabaseConfigProvider.get[JdbcProfile](play.api.Play.current)
import dbConfig.driver.api._
val db = dbConfig.db
</code></pre>

Slick can now be used:

<pre><code class="bash">scala> :paste
// Entering paste mode (ctrl-D to finish)

 case class AccumulationRow(
  id: Long,
  mantraId: Long,
  userId: String,
  gatheringId: Long,
  count: Long,
  year:Int,
  month:Int,
  day:Int)

  class AccumulationTable(tag: Tag) extends Table[AccumulationRow](tag, "accumulations") {
    def id = column[Long]("id", O.AutoInc, O.PrimaryKey)
    def mantraId = column[Long]("mantra_id")
    def userId = column[String]("user_id")
    def gatheringId = column[Long]("gathering_id")
    def count = column[Long]("count")
    def year = column[Int]("year")
    def month = column[Int]("month")
    def day = column[Int]("day")
    def * = (id, mantraId, userId, gatheringId, count, year, month, day) <> (AccumulationRow.tupled, AccumulationRow.unapply)
  }
  val slickAccumulations = TableQuery[AccumulationTable]
  import scala.concurrent.duration._
  import scala.concurrent._
  import models.daos._
  val q = slickAccumulations.length
  Await.result(db.run(q.result), Duration(1000, MILLISECONDS))
</code></pre>