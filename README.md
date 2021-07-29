# Euro-2020-Will-Switzerland-kick-out-Spain-too

One of the big sensations of the UEFA Euro 2020 is that Switzerland kicked out world champion France. We take this as an opportunity to share with you a simple statistical model to predict football (soccer) results with R, so read on!

Football is a highly stochastic game, which is one of the reasons for its appeal: anything can happen! But there are still some known patterns that can be used to build a predictive model.

First, it is well known that the probability for the number of goals in a game can be well approximated by a Poisson distribution.

Second, it is also well known that one of the best predictors of the strength of a team is its market value (in German there is the saying “Geld schießt Tore”, which translates to “money scores goals”). We can find the market value of the different teams e.g. here: transfermarkt.de.

The third ingredient that we need is the average number of goals scored per game. Wikipedia tells us that this is about 2.8 for the current tournament.

The main idea is to divide this average number according to the market values of both teams to get the average number of goals per team and feed that into two Poisson distributions to determine the probabilities for each potential number of goals scored:

mean_total_score <-  2.8 # https://en.wikipedia.org/wiki/UEFA_Euro_2020_statistics

# https://www.transfermarkt.de/europameisterschaft-2020/teilnehmer/pokalwettbewerb/EM20
country1 = "Switzerland"; colour1 <- "red"    ; value1 <- 0.29
country2 = "Spain"      ; colour2 <- "orange" ; value2 <- 0.92

ratio <- value1 / (value1 + value2)
mean_goals1 <- ratio * mean_total_score
mean_goals2 <- (1 - ratio) * mean_total_score

prob_goals1 <- dpois(0:7, mean_goals1)
prob_goals2 <- dpois(0:7, mean_goals2)

parbkp <- par(mfrow=c(1, 2))
max_ylim <- max(prob_goals1, prob_goals2)
plot(0:7, prob_goals1, type = "h", ylim = c(0, max_ylim), xlab = country1, ylab = "Probability", col = colour1, lwd = 10)
plot(0:7, prob_goals2, type = "h", ylim = c(0, max_ylim), xlab = country2, ylab = "", col = colour2, lwd = 10)
title(paste0(country1, " ", which.max(prob_goals1) - 1, ":", which.max(prob_goals2) - 1, " ", country2), line = -2, outer = TRUE)
par(parbkp)

So the most probable prediction is that Spain will win this one clearly… but you never know! And apart from those hard numbers, many will root for the underdog anyway (me too because I have a special relationship with Switzerland since I did my Ph.D. there at the University of St. Gallen and still have a lot of friends from that time).

I have played around with this simple model for nearly ten years now and it often proved surprisingly accurate. Its biggest shortcoming is of course that it treats both distributions independently. Another one is that it doesn’t include the home advantage (although this effect seems to be fading). A third point is that it is based on only one variable (market value), but there are of course others that are also important (e.g. the ratio of goal shots of both teams or the World Football ELO Ratings).
