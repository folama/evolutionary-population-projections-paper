#Evolutionary bayesTFR code

This document contains the R code used to generate the simulations and charts in the working paper "Natural selection makes world population stabilisation unlikely in the foreseeable future" (link to be provided when available). The simulations use an amended "evolutionary" version of the R package bayesTFR (link). The evolutionary version can be found at [github].

##Initiation

These simulations require the packages bayesTFR, bayesLife and bayesPop. The evolutionary branch of bayesTFR should be installed from github, which requires installation of the package 'devtools'
```
install.packges("devtools")
library("devtools")
install_github(XXX)
```

BayesLife and bayesPop are installed from CRAN.
```
install.packages("bayesLife")
install.packages("bayesPop")
```

All three packages should be loaded.
```
library("bayesTFR")
library("bayesLife")
library("bayesPop")
```

##Base model

To generate the base model forecasts, we ran a converged simulation with 3x62,000 long MCMCs of Phase II and 3x70,000 long MCMCs of Phase III.
```
m <- run.tfr.mcmc(iter='auto', thin=30, nr.chains=3, start.year=1950, present.year=2015, wpp.year=2015, seed=20160529, output.dir='[base simulation directory]', verbose=TRUE)
m3 <- run.tfr3.mcmc(iter='auto', thin=30, nr.chains=3, seed=20160529, sim.dir='[base simulation directory]', output.dir='[base simulation directory]', verbose=TRUE)
```

We generated fertility preditions and stored them in the element `predtfr`. Fertility projections for the base model can also be accessed from the predictions folder in the [base simulation directory].
```
tfr.predict(evo=FALSE, end.year=2100, burnin=2000, burnin3=2000, seed=20160529, nr.traj=1000, sim.dir='[base simulation directory]', use.correlation=TRUE, save.as.ascii=0, use.tfr3=TRUE)
predtfr <- get.tfr.prediction(sim.dir='[base simulation directory]')
```

We generated base population projections and stored them in the element `predpop`. We used life expectancy projections from WPP2015. Tables of results are available in the [base table directory].
```
pop.predict(end.year=2100, start.year=1950, present.year=2015, nr.traj=1000, inputs = list(tfr.sim.dir='[base simulation directory]'), output.dir='[base simulation directory]', verbose=TRUE, wpp.year=2015)
predpop <- get.pop.prediction(sim.dir='[base simulation directory]')
write.pop.projection.summary(predpop, what=c('pop', 'popsexage'), output.dir='[base table directory]')
```

We generated population support ratios from the population data.
```
write.pop.projection.summary(predpop, expression="PXXX[5:13]/PXXX[14:27]", output.dir='[base table directory]', file.suffix='PSR')
write.pop.projection.summary(predpop, expression="PXXX[5:13]/(PXXX[1:4]+PXXX[14:27])", output.dir='[base table directory]', file.suffix='TPSR')
```

We generated world and continental population aggregates separately.
```
aggr <- pop.aggregate(predpop, c(900, 903, 904, 905, 908, 909, 935))
write.pop.projection.summary(aggr, what=c('pop', 'popsexage'), output.dir='[base table directory]', file.suffix='world')
write.pop.projection.summary(aggr, expression="PXXX[5:13]/PXXX[14:27]", output.dir='[base table directory]', file.suffix='PSR aggregate')
write.pop.projection.summary(aggr, expression="PXXX[5:13]/(PXXX[1:4] + PXXX[14:27])", output.dir='[base table directory]', file.suffix='TPSR aggregate')
```

##Evolutionary model

Before generating the evolutionary model predictions, we copied the MCMC data generated for the base predictions into the [evolutionary simulation directory]. We did this by creating a replica of the [base simulation directory].

We generated the evolutionary fertility predictions by setting `evo=TRUE` and stored them in the element `evopredtfr`. Fertility projection from the base model can now be accessed from the predictions folder in the [base simulation directory].
```
tfr.predict(evo=TRUE, h2=0.2, end.year=2100, burnin=2000, burnin3=2000, seed=20160529, nr.traj=1000, sim.dir='[evolutionary simulation directory]', use.correlation=TRUE, save.as.ascii=0, use.tfr3=TRUE, replace.output=TRUE)
evopredtfr <- get.tfr.prediction(sim.dir='[evolutionary simulation directory]')
```

We then generated evolutionary population projections and stored them in the element `evopredpop`. We used life expectancy data from WPP2015. Tables of results are available in the [evolutionary table directory].
```
pop.predict(end.year=2100, start.year=1950, present.year=2015, nr.traj=1000, inputs = list(tfr.sim.dir='[evolutionary simulation directory]'), output.dir='[evolutionary simulation directory]', verbose=TRUE, wpp.year=2015)
evopredpop <- get.pop.prediction(sim.dir='[evolutionary simulation directory]')
write.pop.projection.summary(evopredpop, what=c('pop', 'popsexage'), output.dir='[evolutionary table directory]')
```

We also generated population support ratios for the evolutionary model.
```
pop.trajectories.plotAll (evopredpop, nr.traj=0, expression="PXXX[5:13]/PXXX[14:27]", output.dir='[evolutionary table directory]', output.type='jpeg', verbose=TRUE )
write.pop.projection.summary(evopredpop, expression="PXXX[5:13]/PXXX[14:27]", output.dir='[evolutionary table directory]', file.suffix='PSR')
pop.trajectories.plotAll (evopredpop, nr.traj=0, expression="PXXX[5:13]/(PXXX[1:4]+PXXX[14:27])", output.dir='[evolutionary table directory]', output.type='jpeg', verbose=TRUE )
write.pop.projection.summary(evopredpop, expression="PXXX[5:13]/(PXXX[1:4]+PXXX[14:27])", output.dir='[evolutionary table directory]', file.suffix='TPSR')
```

We generated world and continental population aggregates for the evolutionary model separately.
```
evoaggr <- pop.aggregate(evopredpop, c(900, 903, 904, 905, 908, 909, 935))
write.pop.projection.summary(evoaggr, what=c('pop', 'popsexage'), output.dir='[evolutionary table directory]', file.suffix='world')
write.pop.projection.summary(evoaggr, expression="PXXX[5:13]/PXXX[14:27]", output.dir='[evolutionary table directory]', file.suffix='PSR aggregate')
write.pop.projection.summary(evoaggr, expression="PXXX[5:13]/(PXXX[1:4]+PXXX[14:27])", output.dir='[evolutionary table directory]', file.suffix='TPSR aggregate')
```

##Charts

We generated charts of the total fertility rate, with the base and evolutionary model plotted on the same figure. The country code was changed that of the area for which we wished to generate the chart. The country codes are:

* World 900
* Africa 903
* Latin America and the Caribbean 904
* Northern America 905
* Europe 908
* Oceania 909
* Asia 935

```
tfr.trajectories.plot(evopredtfr, country=900, pi=c(90), nr.traj=0, half.child.variant=FALSE, typical.trajectory=FALSE, show.legend = FALSE, ylim=NULL)
tfr.trajectories.plot(predtfr, country=900, pi=c(90), nr.traj=0, col=c('black', 'green', 'blue', 'blue', 'blue', '#00000020'), half.child.variant=FALSE, typical.trajectory=FALSE, show.legend = FALSE, add=TRUE)
```

We then generated charts of the population size, with the base and evolutionary model plotted on the same figure.
```
pop.trajectories.plot (evoaggr, country=900, pi=c(90), nr.traj=0, sum.over.ages=TRUE, show.legend=FALSE)
pop.trajectories.plot (aggr, country=900, pi=c(90), nr.traj=0, col=c('black', 'blue', 'blue', 'blue', 'blue', '#00000020'), sum.over.ages=TRUE, show.legend=FALSE, add=TRUE)

```