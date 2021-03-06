# My-Skills

---
title: "A Sankey diagram of my skills"
author: "John Blum"
date: "8/6/2021"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = FALSE, warning = FALSE, message=FALSE)
```


# Objective
The purpose of this exercise was to take stock of my job skills following graduation from Georgetown (McDonough School of Business and Walsh School of Foreign Service), with a Master's in International Business and Policy. I wanted to juxtapose those skills against the science skills from my PhD a decade earlier, as well as identify with broader skills I've always had. Familiar with a Sankey diagram, it seemed to be one way to visualize what I could otherwise put on paper.

# Data
I created lists of hard and soft skills, put them into categories, and mapped how relevant each were for each job role I have had. This was done manually in excel in a matrix that could be used to construct the Sankey diagram. For each job role, I listed the skills and then caculated the percentage each skill was used for that job role. I also weighted each job role. I did not apply a time weight, for example my time at Georgetown was not weighted as 1/7th my time at UCSD.



```{r fig.width=8,fig.height=8}

# Load data

library(readxl)
library(tidyverse)
library(networkD3)
library(kableExtra)


# load data

df <- read_xlsx("Skills.xlsx",sheet="IncidentMatrix2")
df$...1 <- NULL
rownames(df) <- colnames(df)

kbl(df)

# Transform to connection data frame
links <- df %>% rownames_to_column(var="source") %>%
  gather(key="target",value="value",-1) %>%
  filter(value !=0)

# Create node data frame
nodes <- data.frame(name=c(as.character(links$source),as.character(links$target)) %>% unique()
)

# With networkD3, connection must be provided using id, not using real name like in the links dataframe.. So we need to reformat it.
links$IDsource <- match(links$source, nodes$name)-1 
links$IDtarget <- match(links$target, nodes$name)-1

# groups
nodes$group <- as.factor(c(""))

# colors
#my_color <- c("a6dba0",	"a6dba0",	"a6dba0",	"a6dba0",	"a6dba0",	"#1b7837",	"#1b7837",	"#1b7837",	"#1b7837",	"#1b7837",	"#1b7837",	"#1b7837",	"#762a83",	"#762a83",	"#762a83",	"#762a83",	"#762a83",	"#762a83",	"#762a83",	"#762a83",	"#762a83",	"#762a83",	"#762a83",	"#5aae61",	"#5aae61",	"#5aae61",	"#5aae61",	"#5aae61",	"#5aae61",	"#5aae61",	"#5aae61",	"#5aae61",	"#9970ab",	"#c2a5cf",	"e7d4e8",	"#f7f7f7",	"#d9f0d3")

```

# Plot the Sankey Diagram
```{r}
 
# Make the Network
p <- sankeyNetwork(Links = links, Nodes = nodes,
                     Source = "IDsource", Target = "IDtarget",
                     Value = "value", NodeID = "name",
                     sinksRight=FALSE)

p


```

In html, these boxes can be moved around and organized or arranged. I note one error: I used leadership twice as originally I scripted these as slightly different skills. Afterwards, I'd prefer to just do it once but I already calculated all the different percentages and have opted not to make this change.

The output looks like this with some annotations added in a photo program.

![alt text](https://github.com/blumjohn/My-Skills/blob/main/Blum_Skills_Sankey.png?raw=true)


# Alternative: Display as a Chord Diagram
Another way of visualizing is to display these skills as a chord diagram.

```{r fig.width=12,fig.height=12 }
library(circlize)

df2 <- read_xlsx("Skills.xlsx",sheet="ChordSkills")
df2$Category <- NULL
df2$Weight <- NULL
df2$Percentage <- NULL

grid.col = c("UCSD" = "grey", "XOM GeOps" = "grey", "XOM URC" = "grey", "BP" = "grey", "Georgetown MSB" = "grey", "Scheduling" = "#f7fbff", "Contracting" = "#deebf7", "Risk" = "#c6dbef", "Budget" = "#9ecae1", "Negotiating" = "#6baed6", "Task" = "#4292c6", "Quality" = "#2171b5", "HSE" = "#08519c","Documentation"="#08306b","Integration"="#dadaeb","Analysis"="#bcbddc", "Programming"="#9e9ac8","Engineering"="#756bb1","Geospatial"="#54278f","Finance"="#e5f5e0","Marketing"="#c7e9c0","Strategy"="#a1d99b","Leadership"="#74c476","Communications"="#41ab5d","Writing"="#238b45","Presenting"="#005a32")

chordDiagram(df2,grid.col=grid.col,transparency = 0.25,annotationTrack="grid",preAllocateTracks = 1)
title("Job Skills")

#circos.clear()
circos.trackPlotRegion(track.index = 1, panel.fun = function(x, y) {
  xlim = get.cell.meta.data("xlim")
  ylim = get.cell.meta.data("ylim")
  sector.name = get.cell.meta.data("sector.index")
  circos.text(mean(xlim), ylim[1] + .1, sector.name, facing = "clockwise", niceFacing = TRUE, adj = c(0, 0.5), col = "Black")
}, bg.border = NA)


```


![alt text](https://github.com/blumjohn/My-Skills/blob/main/skillschorddiagram.png?raw=true)


While this is a nice looking diagram, it's a bit too cluttered and not as effective as the Sankey diagram. 
