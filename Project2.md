---
layout: default
title: "Project 2"
permalink: /projects/project-2/
markdown: kramdown
---

# Various approaches to estimating survival and recovery rates from dead recovery data

This research was inspired by a group project in my third year, in which we estimated the survival and recovery rates of blackbirds and sandwich terns from a BTO dead recovery dataset. We had access to ring recovery data, and used multinomial models to estimate the rates. At the start of the project, I misinterpreted the data I had collected, and ended up creating my own model that, while valid, produced incorrect results. After the project, I found the correct data, and fit a more appropriate model. This page compares both models and contains the ongoing results of my research into this data.

I have not yet commented on the results of the Bayesian estimation.

## Background and introduction

Bird ringing is a method to track birds; a small ring is attached to their legs that can be used to uniquely identify them. If the bird dies and is found by someone and they report the death to the ringing scheme, then the ringers know that that bird has died. In a bird ringing scheme, a cohort of birds are ringed each year, and deaths in subsequent years from each cohort are recorded. This results in a dataset that looks like the table below

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}$</td>
        <td style="border: 1px solid #ccc;">  $R_{11}$</td>
        <td style="border: 1px solid #ccc;">  $R_{12}$</td>
        <td style="border: 1px solid #ccc;">  $R_{13}$</td>
        <td style="border: 1px solid #ccc;">  $R_{14}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $N_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $R_{22}$</td>
        <td style="border: 1px solid #ccc;">  $R_{23}$</td>
        <td style="border: 1px solid #ccc;">  $R_{24}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $N_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $R_{33}$</td>
        <td style="border: 1px solid #ccc;">  $R_{34}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $N_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $R_{44}$</td>
      </tr>
    </tbody>
  </table>
</div>

For example, R<sub>24</sub> is the number of birds that were ringed in year 2 and recovered in year 4.

## Age-stratified data

As birds may survive at different rates at different ages, birds can be recorded as being ringed as an adult, juvenile, or pullus. In this research, a an adult is defined as being a bird older than 1 year of age, a juvenile is defined as being a bird less than 1 year old that has left the nest, and pullus is defined as being a bird still in the nest. By ringing birds as pulli, we can record whether they have died in the so called post-fledging period; this is the stage of life during which the bird has left the nest but is not yet independent of its parents. Birds are often highly vulnerable during this period. 

The 2 models discussed in this research estimate survival and recovery rates during the post-fledging period for pulli recovery data including recoveries during the post-fledging period and after it separately, and data including these 2 values combined. 

For both models, adult and juvenile data is the same, and is formatted as follows.

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="6">Observed recovery counts of birds ringed as adults</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}$</td>
        <td style="border: 1px solid #ccc;">  $R_{11}$</td>
        <td style="border: 1px solid #ccc;">  $R_{12}$</td>
        <td style="border: 1px solid #ccc;">  $R_{13}$</td>
        <td style="border: 1px solid #ccc;">  $R_{14}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $N_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $R_{22}$</td>
        <td style="border: 1px solid #ccc;">  $R_{23}$</td>
        <td style="border: 1px solid #ccc;">  $R_{24}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $N_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $R_{33}$</td>
        <td style="border: 1px solid #ccc;">  $R_{34}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $N_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $R_{44}$</td>
      </tr>
    </tbody>
  </table>
</div>

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="6">Observed recovery counts of birds ringed as juveniles</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $M_{1}$</td>
        <td style="border: 1px solid #ccc;">  $Q_{11}$</td>
        <td style="border: 1px solid #ccc;">  $Q_{12}$</td>
        <td style="border: 1px solid #ccc;">  $Q_{13}$</td>
        <td style="border: 1px solid #ccc;">  $Q_{14}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $M_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $Q_{22}$</td>
        <td style="border: 1px solid #ccc;">  $Q_{23}$</td>
        <td style="border: 1px solid #ccc;">  $Q_{24}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $M_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $Q_{33}$</td>
        <td style="border: 1px solid #ccc;">  $Q_{34}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $M_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $Q_{44}$</td>
      </tr>
    </tbody>
  </table>
</div>

## Separate post-fledging recovery data

### The data

In the case where post-fledging recoveries are recorded separately to juvenile recoveries, the pullus recovery data will be stored as in the table below

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="7">Observed recovery counts of birds ringed as pulli</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="5">$\text{Year recovered}$</th>
      </tr>
      <tr>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="2">$1$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="2">$2$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="2">$3$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="2">$4$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px; background-color: rgba(255, 0, 0, 0.5);">$\text{PF}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px; background-color: rgba(0, 0, 255, 0.5);">$\text{Juv.}$</th>	
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}$</td>
        <td style="border: 1px solid #ccc; background-color: rgba(255, 0, 0, 0.5);">  $P_{11}$</td>
        <td style="border: 1px solid #ccc; background-color: rgba(0, 0, 255, 0.5);">  $P_{12}$</td>
        <td style="border: 1px solid #ccc;">  $P_{13}$</td>
        <td style="border: 1px solid #ccc;">  $P_{14}$</td>
        <td style="border: 1px solid #ccc;">  $P_{15}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc; background-color: rgba(255, 0, 0, 0.5);">  $P_{22}$</td>
        <td style="border: 1px solid #ccc; background-color: rgba(0, 0, 255, 0.5);">  $P_{23}$</td>
        <td style="border: 1px solid #ccc;">  $P_{24}$</td>
        <td style="border: 1px solid #ccc;">  $P_{25}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $L_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc; background-color: rgba(255, 0, 0, 0.5);">  $P_{33}$</td>
        <td style="border: 1px solid #ccc; background-color: rgba(0, 0, 255, 0.5);">  $P_{34}$</td>
        <td style="border: 1px solid #ccc;">  $P_{35}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $L_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc; background-color: rgba(255, 0, 0, 0.5);">  $P_{44}$</td>
        <td style="border: 1px solid #ccc; background-color: rgba(0, 0, 255, 0.5);">  $P_{45}$</td>
      </tr>
    </tbody>
  </table>
</div>

The first cell in each row is the number of birds from cohort <i>i</i> recovered in their post-fledging period; the second is the number of birds from cohort <i>i</i> recovered in their first year after their post-fledging period.

### The model

The data is modelled as coming from a multinomial distribution. I assign one set of parameters for each year of an adults life, a separate set for the first year of life for birds ringed as juveniles, and a separate set for the post-fledging period for birds ringed as pulli.

Under this model, the expected number of recoveries in each cell is given by

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="6">Expected recovery counts of birds ringed as adults</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}f_{1}$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}S_{1}f_{2}$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}S_{1}S_{2}f_{3}$</td>
        <td style="border: 1px solid #ccc;">  $N_{1}S_{1}S_{2}S_{3}f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $N_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $N_{2}f_{2}$</td>
        <td style="border: 1px solid #ccc;">  $N_{2}S_{2}f_{3}$</td>
        <td style="border: 1px solid #ccc;">  $N_{2}S_{2}S_{3}f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $N_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $N_{3}f_{3}$</td>
        <td style="border: 1px solid #ccc;">  $N_{3}S_{3}f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $N_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $N_{4}f_{4}$</td>
      </tr>
    </tbody>
  </table>
</div>

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="6">Expected recovery counts of birds ringed as juveniles</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $M_{1}$</td>
        <td style="border: 1px solid #ccc;">  $M_{1}f_{1}' $</td>
        <td style="border: 1px solid #ccc;">  $M_{1}S_{1}' f_{2}$</td>
        <td style="border: 1px solid #ccc;">  $M_{1}S_{1}' S_{2}f_{3}$</td>
        <td style="border: 1px solid #ccc;">  $M_{1}S_{1}' S_{2}S_{3}f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $M_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $M_{2}f_{2}' $</td>
        <td style="border: 1px solid #ccc;">  $M_{2}S_{2}' f_{3}$</td>
        <td style="border: 1px solid #ccc;">  $M_{2}S_{2}' S_{3}f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $M_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $M_{3}f_{3}' $</td>
        <td style="border: 1px solid #ccc;">  $M_{3}S_{3}' f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $M_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $M_{4}f_{4}' $</td>
      </tr>
    </tbody>
  </table>
</div>

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="7">Expected recovery counts of birds ringed as pulli</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="5">$\text{Year recovered}$</th>
      </tr>
      <tr>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="2">$1$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="2">$2$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="2">$3$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="2">$4$</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$\text{PF}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">$\text{Juv.}$</th>	
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}f_{1}'' $</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' f_{1}' $</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' f_{2}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' S_{2}f_{3}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' S_{2}S_{3}f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{2}f_{2}'' $</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' f_{2}' $</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' S_{2}' f_{3}$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' S_{2}' S_{3}f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $L_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{3}f_{3}'' $</td>
        <td style="border: 1px solid #ccc;">  $L_{3}S_{3}'' f_{3}' $</td>
        <td style="border: 1px solid #ccc;">  $L_{3}S_{3}'' S_{3}' f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $L_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{4}f_{4}'' $</td>
        <td style="border: 1px solid #ccc;">  $L_{4}S_{4}'' f_{4}' $</td>
      </tr>
    </tbody>
  </table>
</div>

### The likelihood

This model gives the row-wise likelihood functions

$${::nomarkdown}
\begin{aligned}
\ln\left(\mathcal{L}_{p}\right) &\propto R_{pp}\ln(f_{p}) + R_{p(p+1)}\ln(S_{p}f_{p+1}) + \dots + R_{pi}\ln(S_{p}S_{p+1}\dots S_{i-1}f_{i}) + \\ &\quad R_{p(i+1)}\ln(S_{p}S_{p+1}\dots S_{i-1}S_{i}f_{i+1}) + \dots + R_{pl}\ln(S_{p}S_{p+1}\dots S_{l-1}f_{l}) + (N_{p} - R_{p.})\ln(1 - \theta_{p}) \\
\ln\left(\mathcal{L}'_{p}\right) &\propto Q_{pp}\ln(f'_{p}) + Q_{p(p+1)}\ln(S'_{p}f_{p+1}) + \dots + Q_{pi}\ln(S'_{p}S_{p+1}\dots S_{i-1}f_{i}) + \\ &\quad Q_{p(i+1)}\ln(S'_{p}S_{p+1}\dots S_{i-1}S_{i}f_{i+1}) + \dots + Q_{pl}\ln(S'_{p}S_{p+1}\dots S_{l-1}f_{l}) + (M_{p} - Q_{p.})\ln(1 - \theta'_{p}) \\
\ln\left(\mathcal{L}''_{p}\right) &\propto P_{pp}\ln(f''_{p}) + P_{p(p+1)}\ln(S''_{p}f'_{p}) + \dots + P_{p(i+1)}\ln(S''_{p}S'_{p}S_{p+1}\dots S_{i-1}f_{i}) + \\ &\quad P_{p(i+2)}\ln(S''_{p}S'_{p}S_{p+1}\dots S_{i-1}S_{i}f_{i+1}) + \dots + P_{p(l+1)}\ln(S''_{p}S'_{p}S_{p+1}\dots S_{l-1}f_{l}) + (L_{p} - P_{p.})\ln(1 - \theta''_{p})
\end{aligned}
{:/}$$

where

$${::nomarkdown}
\begin{aligned}
\theta_{p} &= f_{p} + S_{p}f_{p+1} + S_{p}S_{p+1}f_{p+2} + S_{p}S_{p+1}S_{p+2}f_{p+3} + \dots + \left(S_{p}S_{p+1}\dots S_{i-1}f_{i}\right) \\ &\quad + \left(S_{p}S_{p+1}\dots S_{i-1}S_{i}f_{i+1}\right) + \dots + \left(S_{p}S_{p+1}\dots S_{l-1}f_{l}\right) \\
\theta'_{p} &= f'_{p} + S'_{p}f_{p+1} + S'_{p}S_{p+1}f_{p+2} + S'_{p}S_{p+1}S_{p+2}f_{p+3} + \dots + \left(S'_{p}S_{p+1}\dots S_{i-1}f_{i}\right) \\ &\quad+ \left(S'_{p}S_{p+1}\dots S_{i-1}S_{i}f_{i+1}\right) + \dots + \left(S'_{p}S_{p+1}\dots S_{l-1}f_{l}\right) \\
\theta''_{p} &= f''_{p} + S''_{p}f'_{p} + S''_{p}S'_{p}f_{p+1} + S''_{p}S'_{p}S_{p+1}f_{p+2} + \dots + \left(S''_{p}S'_{p}S_{p+1}\dots S_{i-1}f_{i}\right) \\ &\quad + \left(S''_{p}S'_{p}S_{p+1}\dots S_{i-1}S_{i}f_{i+1}\right) + \dots + \left(S''_{p}S'_{p}S_{p+1}\dots S_{l-1}f_{l}\right)
\end{aligned}
{:/}$$

are the total probabilities of recovery for cohort $p$. From this, we define

$${::nomarkdown}
\begin{aligned}
\ln\left(\mathcal{L}\right) &= \sum_{i=1}^{l}\mathcal{L}_{i} \\
\ln\left(\mathcal{L'}\right) &= \sum_{i=1}^{l}\mathcal{L'}_{i} \\
\ln\left(\mathcal{L''}\right) &= \sum_{i=1}^{l}\mathcal{L''}_{i} \\
\end{aligned}
{:/}$$

so that the total likelihood is given by

$${::nomarkdown}
\mathscr{L} = \ln\left(\mathcal{L}\right) + \ln\left(\mathcal{L'}\right) + \ln\left(\mathcal{L''}\right)
{:/}$$

### MLEs

Let

$${::nomarkdown}
\begin{aligned}
Z_{i} &= T_{i} + U_{i} - Q_{i.} + V_{i} - P_{i.} \\
W_{i} &= R_{.i} + Q_{.i} - Q_{ii} + P_{.i} - P_{ii} - P_{i(i+1)} \\
C_{i} &= Q_{i.} + P_{i.} - P_{ii} \\
D_{i} &= Q_{ii} + P_{i(i+1)}
\end{aligned}
{:/}$$

Then maximising the likelihood yields the MLEs

$${::nomarkdown}
\begin{aligned}
\hat{S}_{i} &= \frac{R_{i.}}{N_{i}}\frac{N_{i+1}}{R_{(i+1).}}\frac{Z_{i} - W_{i}}{Z_{i}} \\
\hat{f}_{i} &= \frac{R_{i.}}{N_{i}}\frac{W_{i}}{Z_{i}} \\
\hat{S}'_{i} &= \frac{Q_{i.}}{M_{i}}\frac{N_{i+1}}{R_{(i+1).}}\frac{C_{i} - D_{i}}{C_{i}}  \\
\hat{f}'_{i} &= \frac{Q_{i.}}{M_{i}}\frac{D_{i}}{C_{i}} \\
\hat{S}''_{i} &= \frac{M_{i}}{L_{i}}\frac{P_{i.} - P_{ii}}{Q_{i.}} \\
\hat{f}''_{i} &= \frac{P_{ii}}{L_{i}}
\end{aligned}
{:/}$$

### Variances

By the multivariate delta method, we obtain variances

$${::nomarkdown}
\begin{aligned}
\text{Var}\left(\hat{S}_{i}\right) &= \hat{S_{i}}^{2}\left(\frac{1}{R_{i.}} - \frac{1}{N_{i}} + \frac{1}{R_{i+1.}} - \frac{1}{N_{i+1}} + \frac{1}{Z_{i} - W_{i}} - \frac{1}{Z_{i}}\right)\\
\text{Var}\left(\hat{f}_{i}\right) &= \hat{f_{i}}^{2}\left(\frac{1}{R_{i.}} - \frac{1}{N_{i}} + \frac{1}{W_{i}} - \frac{1}{Z_{i}}\right) \\
\text{Var}\left(\hat{S}'_{i}\right) &= \hat{S_{i}}^{2}\left(\frac{1}{Q_{i.}} - \frac{1}{M_{i}} + \frac{1}{R_{i+1.}} - \frac{1}{N_{i+1}} + \frac{1}{C_{i} - D_{i}} - \frac{1}{C_{i}}\right) \\
\text{Var}\left(\hat{f}'_{i}\right) &= \hat{f_{i}}^{2}\left(\frac{1}{Q_{i.}} - \frac{1}{M_{i}} + \frac{1}{D_{i}} - \frac{1}{C_{i}}\right) \\
\text{Var}\left(\hat{S}''_{i}\right) &= \hat{S''_{i}}^{2}\left(\frac{1}{Q_{i.}} - \frac{1}{M_{i}} + \frac{1}{P_{i.} - P_{ii}} - \frac{1}{L_{i}}\right) \\
\text{Var}\left(\hat{f}''_{i}\right) &= \hat{f''_{i}}^{2}\left(\frac{1}{P_{ii}} - \frac{1}{L_{i}}\right) \\\\
\end{aligned}
{:/}$$

## Combined post-fledging recovery data

### The data

In the case where post-fledging recoveries are <i>not</i> recorded separately to juvenile recoveries for birds ringed as pulli, the pullus recovery data will be stored as in the table below

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="6">Observed recovery counts of birds ringed as pulli</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}$</td>
        <td style="border: 1px solid #ccc;">  $P_{11}$</td>
        <td style="border: 1px solid #ccc;">  $P_{12}$</td>
        <td style="border: 1px solid #ccc;">  $P_{13}$</td>
        <td style="border: 1px solid #ccc;">  $P_{14}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $P_{22}$</td>
        <td style="border: 1px solid #ccc;">  $P_{23}$</td>
        <td style="border: 1px solid #ccc;">  $P_{24}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $L_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $P_{33}$</td>
        <td style="border: 1px solid #ccc;">  $P_{34}$</td>

      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $L_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $P_{44}$</td>
      </tr>
    </tbody>
  </table>
</div> 

The first cell in each row is the number of birds from cohort $i$ recovered in their first year of life, irrespective of time of recovery.

### The model

Under the combined post-fledging model, adults and juveniles are modelled identically as above, but the expected counts of pullus recoveries are given by the table below

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th colspan="6">Expected recovery counts of birds ringed as pulli</th>
      </tr>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Year marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" rowspan="3">$\text{Number marked}$</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;" colspan="4">$\text{Year recovered}$</th>
      </tr>
      <tr>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$1$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$2$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$3$</th>
	<th style="border: 1px solid #ccc; padding: 6px 12px;">$4$</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc;">  $1$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}(f_{1}'' + S_{1}'' f_{1}')$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' f_{2}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' S_{2}f_{3}$</td>
        <td style="border: 1px solid #ccc;">  $L_{1}S_{1}'' S_{1}' S_{2}S_{3}f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $2$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{2}(f_{2}'' + S_{2}'' f_{2}')$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' S_{2}' f_{3}$</td>
        <td style="border: 1px solid #ccc;">  $L_{2}S_{2}'' S_{2}' S_{3} f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $3$</td>
        <td style="border: 1px solid #ccc;">  $L_{3}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{3}(f_{3}'' + S_{3}'' f_{3}')$</td>
        <td style="border: 1px solid #ccc;">  $L_{3}S_{3}'' S_{3}' f_{4}$</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc;">  $4$</td>
        <td style="border: 1px solid #ccc;">  $L_{4}$</td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  </td>
        <td style="border: 1px solid #ccc;">  $L_{4}(f_{4}'' + S_{4}'' f_{4})$</td>
      </tr>
    </tbody>
  </table>
</div>

### The likelihood

This model gives the row-wise likelihoods

$${::nomarkdown}
\begin{aligned}
\ln\left(\mathcal{L}_{p}\right) &\propto R_{pp}\ln(f_{p}) + R_{p(p+1)}\ln(S_{p}f_{p+1}) + \dots + R_{pi}\ln(S_{p}S_{p+1}\dots S_{i-1}f_{i}) + \\ &\quad R_{p(i+1)}\ln(S_{p}S_{p+1}\dots S_{i-1}S_{i}f_{i+1}) + \dots + R_{pl}\ln(S_{p}S_{p+1}\dots S_{l-1}f_{l}) + (N_{p} - R_{p.})\ln(1 - \theta_{p}) \\
\ln\left(\mathcal{L}'_{p}\right) &\propto Q_{pp}\ln(f'_{p}) + Q_{p(p+1)}\ln(S'_{p}f_{p+1}) + \dots + Q_{pi}\ln(S'_{p}S_{p+1}\dots S_{i-1}f_{i}) + \\ &\quad Q_{p(i+1)}\ln(S'_{p}S_{p+1}\dots S_{i-1}S_{i}f_{i+1}) + \dots + Q_{pl}\ln(S'_{p}S_{p+1}\dots S_{l-1}f_{l}) + (M_{p} - Q_{p.})\ln(1 - \theta'_{p}) \\
\ln\left(\mathcal{L}''_{p}\right) &\propto P_{pp}\ln(f''_{p} + S''_{p}f'_{p}) + P_{p(p+1)}\ln(S''_{p}S'_{p}f_{p+1}) + \dots + P_{pi}\ln(S''_{p}S'_{p}S_{p+1}\dots S_{i-1}f_{i}) + \\ &\quad P_{p(i+1)}\ln(S''_{p}S'_{p}S_{p+1}\dots S_{i-1}S_{i}f_{i+1}) + \dots + P_{pl}\ln(S''_{p}S'_{p}S_{p+1}\dots S_{l-1}f_{l}) + (L_{p} - P_{p.})\ln(1 - \theta''_{p})
\end{aligned}
{:/}$$

where $\theta_{i}$, $\theta'_{i}$, and $\theta''_{i}$ are the same as above. The total likelihood is given by summing these likelihoods over all rows, then adding them.

### MLEs

Let

$${::nomarkdown}
\begin{aligned}
Z_{i} &= T_{i} + U_{i} - Q_{i.} + V_{i} - P_{i.} \\
W_{i} &= R_{.i} + Q_{.i} - Q_{ii} + P_{.i} - P_{ii}
\end{aligned}
{:/}$$

Then maximising the likelihood yields

$${::nomarkdown}
\begin{aligned}
\hat{f}''_{i} &= \frac{P_{ii}Q_{i.} - P_{i.}Q_{ii}}{L_{i}(Q_{i.} - Q_{ii})} \\
\hat{S}''_{i} &= \frac{M_{i}}{L_{i}} \frac{P_{i.} - P_{ii}}{Q_{i.} - Q_{ii}} \\
\hat{f}'_{i}  &= \frac{Q_{ii}}{M_{i}} \\
\hat{S}'_{i}  &= \frac{N_{i+1}}{R_{i+1.}}\frac{Q_{i.} - Q_{ii}}{M_{i}} \\
\hat{f}_{i}   &= \frac{R_{i.}}{N_{i}}\frac{W_{i}}{Z_{i}} \\
\hat{S}_{i}   &= \frac{R_{i.}N_{i+1}}{N_{i}R_{i+1.}}\frac{Z_{i} - W_{i}}{Z_{i}}
\end{aligned}
{:/}$$

### Variances

By the multivariate delta method, we obtain variances

$${::nomarkdown}
\begin{aligned}
\text{Var}(\hat{f}''_{i}) &= \hat{f''_{i}}^{2}\left(\frac{1}{Q_{i.} - Q_{ii}} - \frac{1}{L_{i}} + \frac{Q_{i.}P_{ii}(Q_{i.} - P_{ii}) + P_{i.}Q_{ii}(Q_{ii} + P_{i.}) - 2Q_{ii}P_{ii}Q_{i.}}{(P_{ii}Q_{i.} - P_{i.}Q_{ii})^{2}}\right) \\
\text{Var}(\hat{S}''_{i}) &= \hat{S''_{i}}^{2}\left(\frac{1}{Q_{i.} - Q_{ii}} -\frac{1}{M_{i}} + \frac{1}{P_{i.} - P_{ii}} - \frac{1}{L_{i}}\right)\\
\text{Var}(\hat{f}'_{i})  &= \hat{f'_{i}}^{2}\left(\frac{1}{Q_{ii}} - \frac{1}{M_{i}}\right) \\
\text{Var}(\hat{S}'_{i})  &= \hat{S'_{i}}^{2}\left(\frac{1}{R_{i+1.}} - \frac{1}{N_{i+1}} + \frac{1}{Q_{i.} - Q_{ii}} - \frac{1}{M_{i}}\right) \\
\text{Var}(\hat{f}_{i})   &= \hat{f_{i}}^{2}\left(\frac{1}{R_{i.}} - \frac{1}{N_{i}} + \frac{1}{W_{i}} - \frac{1}{Z_{i}}\right) \\
\text{Var}(\hat{S}_{i})   &= \hat{S_{i}}^{2}\left(\frac{1}{R_{i.}} - \frac{1}{N_{i}} + \frac{1}{R_{i+1.}} - \frac{1}{N_{i+1}} + \frac{1}{Z_{i} - W_{i}} - \frac{1}{Z_{i}}\right)
\end{aligned}
{:/}$$

## An example

### The data

The blackbird data collected from 1964-1983 used in Robinson(2012) is shown below

<style>
table td {
  border: 1px solid #ccc;
}
table th {
  border: 1px solid #ccc; padding: 6px 12px;
}
</style>

<details>
<summary>Birds ringed as adults</summary>
<div style="text-align: center; margin: 1em 0;">
<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
  <thead>
    <tr>
      <th colspan="22">Birds ringed as adults</th>
    </tr>
    <tr>
      <th rowspan="2">Year of ringing</th>
      <th colspan="20">Year recovered</th>
      <th rowspan="2">Total ringed</th>
    </tr>
    <tr>
      <th>1</th><th>2</th><th>3</th><th>4</th><th>5</th>
      <th>6</th><th>7</th><th>8</th><th>9</th><th>10</th>
      <th>11</th><th>12</th><th>13</th><th>14</th><th>15</th>
      <th>16</th><th>17</th><th>18</th><th>19</th><th>20</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>1964</td><td>39</td><td>18</td><td>19</td><td>8</td><td>9</td><td>3</td><td>0</td><td>2</td><td>5</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>1891</td></tr>
<tr><td>1965</td><td>0</td><td>44</td><td>23</td><td>16</td><td>15</td><td>16</td><td>4</td><td>6</td><td>1</td><td>1</td><td>0</td><td>0</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>2344</td></tr>
<tr><td>1966</td><td>0</td><td>0</td><td>32</td><td>27</td><td>10</td><td>11</td><td>10</td><td>6</td><td>3</td><td>4</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>2354</td></tr>
<tr><td>1967</td><td>0</td><td>0</td><td>0</td><td>42</td><td>29</td><td>24</td><td>12</td><td>10</td><td>6</td><td>4</td><td>4</td><td>4</td><td>3</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>2993</td></tr>
<tr><td>1968</td><td>0</td><td>0</td><td>0</td><td>0</td><td>38</td><td>31</td><td>15</td><td>14</td><td>11</td><td>4</td><td>6</td><td>5</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>2866</td></tr>
<tr><td>1969</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>62</td><td>29</td><td>18</td><td>20</td><td>15</td><td>12</td><td>8</td><td>4</td><td>2</td><td>2</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>2897</td></tr>
<tr><td>1970</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>47</td><td>25</td><td>21</td><td>20</td><td>18</td><td>7</td><td>3</td><td>3</td><td>0</td><td>2</td><td>0</td><td>0</td><td>0</td><td>0</td><td>3056</td></tr>
<tr><td>1971</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>32</td><td>39</td><td>20</td><td>15</td><td>11</td><td>13</td><td>3</td><td>2</td><td>4</td><td>0</td><td>0</td><td>0</td><td>0</td><td>3311</td></tr>
<tr><td>1972</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>38</td><td>28</td><td>25</td><td>20</td><td>14</td><td>9</td><td>3</td><td>3</td><td>0</td><td>0</td><td>1</td><td>0</td><td>3643</td></tr>
<tr><td>1973</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>42</td><td>33</td><td>24</td><td>17</td><td>10</td><td>7</td><td>5</td><td>1</td><td>1</td><td>0</td><td>0</td><td>3763</td></tr>
<tr><td>1974</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>35</td><td>28</td><td>26</td><td>16</td><td>11</td><td>3</td><td>6</td><td>2</td><td>0</td><td>0</td><td>3668</td></tr>
<tr><td>1975</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>37</td><td>25</td><td>15</td><td>10</td><td>9</td><td>10</td><td>4</td><td>4</td><td>0</td><td>4931</td></tr>
<tr><td>1976</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>36</td><td>31</td><td>19</td><td>12</td><td>8</td><td>6</td><td>1</td><td>2</td><td>4317</td></tr>
<tr><td>1977</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>41</td><td>23</td><td>17</td><td>15</td><td>11</td><td>2</td><td>2</td><td>4070</td></tr>
<tr><td>1978</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>38</td><td>16</td><td>18</td><td>13</td><td>10</td><td>8</td><td>4053</td></tr>
<tr><td>1979</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>41</td><td>31</td><td>28</td><td>7</td><td>9</td><td>3936</td></tr>
<tr><td>1980</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>50</td><td>38</td><td>23</td><td>16</td><td>4505</td></tr>
<tr><td>1981</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>48</td><td>26</td><td>30</td><td>4820</td></tr>
<tr><td>1982</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>25</td><td>21</td><td>4080</td></tr>
<tr><td>1983</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>41</td><td>4629</td></tr>
  </tbody>
</table>
</div>
</details>

<details>
<summary>Birds ringed as juveniles</summary>
<div style="text-align: center; margin: 1em 0;">
<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
  <thead>
    <tr>
      <th colspan="22">Birds ringed as juveniles</th>
    </tr>
    <tr>
      <th rowspan="2">Year of ringing</th>
      <th colspan="20">Year recovered</th>
      <th rowspan="2">Total ringed</th>
    </tr>
    <tr>
      <th>1</th><th>2</th><th>3</th><th>4</th><th>5</th>
      <th>6</th><th>7</th><th>8</th><th>9</th><th>10</th>
      <th>11</th><th>12</th><th>13</th><th>14</th><th>15</th>
      <th>16</th><th>17</th><th>18</th><th>19</th><th>20</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>1964</td><td>114</td><td>39</td><td>17</td><td>12</td><td>10</td><td>12</td><td>8</td><td>5</td><td>3</td><td>3</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4185</td></tr>
<tr><td>1965</td><td>0</td><td>93</td><td>26</td><td>31</td><td>19</td><td>16</td><td>6</td><td>7</td><td>3</td><td>6</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4411</td></tr>
<tr><td>1966</td><td>0</td><td>0</td><td>70</td><td>32</td><td>19</td><td>20</td><td>8</td><td>5</td><td>4</td><td>4</td><td>2</td><td>1</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>3954</td></tr>
<tr><td>1967</td><td>0</td><td>0</td><td>0</td><td>85</td><td>34</td><td>21</td><td>25</td><td>13</td><td>11</td><td>4</td><td>2</td><td>4</td><td>3</td><td>0</td><td>2</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4865</td></tr>
<tr><td>1968</td><td>0</td><td>0</td><td>0</td><td>0</td><td>75</td><td>30</td><td>34</td><td>18</td><td>16</td><td>8</td><td>11</td><td>5</td><td>6</td><td>2</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>5232</td></tr>
<tr><td>1969</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>76</td><td>30</td><td>28</td><td>25</td><td>20</td><td>9</td><td>7</td><td>5</td><td>5</td><td>2</td><td>2</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4918</td></tr>
<tr><td>1970</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>61</td><td>27</td><td>23</td><td>21</td><td>13</td><td>6</td><td>7</td><td>4</td><td>3</td><td>2</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4114</td></tr>
<tr><td>1971</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>53</td><td>34</td><td>15</td><td>14</td><td>11</td><td>10</td><td>4</td><td>8</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>3715</td></tr>
<tr><td>1972</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>74</td><td>21</td><td>17</td><td>14</td><td>11</td><td>8</td><td>9</td><td>6</td><td>3</td><td>1</td><td>1</td><td>0</td><td>3943</td></tr>
<tr><td>1973</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>81</td><td>28</td><td>25</td><td>19</td><td>14</td><td>13</td><td>9</td><td>6</td><td>5</td><td>2</td><td>2</td><td>4071</td></tr>
<tr><td>1974</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>67</td><td>16</td><td>11</td><td>11</td><td>11</td><td>10</td><td>2</td><td>1</td><td>0</td><td>0</td><td>3300</td></tr>
<tr><td>1975</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>71</td><td>33</td><td>17</td><td>15</td><td>7</td><td>6</td><td>1</td><td>5</td><td>0</td><td>4020</td></tr>
<tr><td>1976</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>68</td><td>28</td><td>17</td><td>11</td><td>13</td><td>5</td><td>8</td><td>4</td><td>4498</td></tr>
<tr><td>1977</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>82</td><td>34</td><td>23</td><td>13</td><td>9</td><td>7</td><td>3</td><td>5031</td></tr>
<tr><td>1978</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>74</td><td>30</td><td>13</td><td>15</td><td>8</td><td>7</td><td>4615</td></tr>
<tr><td>1979</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>75</td><td>29</td><td>17</td><td>17</td><td>13</td><td>4997</td></tr>
<tr><td>1980</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>61</td><td>35</td><td>24</td><td>13</td><td>5536</td></tr>
<tr><td>1981</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>62</td><td>31</td><td>22</td><td>5417</td></tr>
<tr><td>1982</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>59</td><td>36</td><td>5471</td></tr>
<tr><td>1983</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>99</td><td>6843</td></tr>
  </tbody>
</table>
</div>
</details>

<details>
<summary>Birds ringed as pulli(sep)</summary>
<div style="text-align: center; margin: 1em 0;">
<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
  <thead>
    <tr>
      <th colspan="23">Birds ringed as pulli(sep)</th>
    </tr>
    <tr>
      <th rowspan="2">Year of ringing</th>
      <th colspan="21">Year recovered</th>
      <th rowspan="2">Total ringed</th>
    </tr>
    <tr>
      <th>PF</th><th>Juv.</th><th>2</th><th>3</th><th>4</th><th>5</th>
      <th>6</th><th>7</th><th>8</th><th>9</th><th>10</th>
      <th>11</th><th>12</th><th>13</th><th>14</th><th>15</th>
      <th>16</th><th>17</th><th>18</th><th>19</th><th>20</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>1964</td><td>52</td><td>15</td><td>10</td><td>2</td><td>8</td><td>5</td><td>3</td><td>1</td><td>0</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>2391</td></tr>
<tr><td>1965</td><td>0</td><td>74</td><td>30</td><td>14</td><td>18</td><td>7</td><td>12</td><td>2</td><td>1</td><td>3</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>3420</td></tr>
<tr><td>1966</td><td>0</td><td>0</td><td>78</td><td>29</td><td>20</td><td>11</td><td>4</td><td>4</td><td>2</td><td>0</td><td>2</td><td>1</td><td>0</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4366</td></tr>
<tr><td>1967</td><td>0</td><td>0</td><td>0</td><td>67</td><td>22</td><td>12</td><td>4</td><td>7</td><td>4</td><td>2</td><td>2</td><td>1</td><td>4</td><td>1</td><td>2</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4187</td></tr>
<tr><td>1968</td><td>0</td><td>0</td><td>0</td><td>0</td><td>101</td><td>30</td><td>10</td><td>8</td><td>8</td><td>6</td><td>3</td><td>6</td><td>1</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4172</td></tr>
<tr><td>1969</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>81</td><td>27</td><td>10</td><td>11</td><td>10</td><td>4</td><td>1</td><td>3</td><td>1</td><td>3</td><td>1</td><td>0</td><td>0</td><td>1</td><td>0</td><td>0</td><td>4364</td></tr>
<tr><td>1970</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>58</td><td>15</td><td>8</td><td>5</td><td>4</td><td>4</td><td>3</td><td>2</td><td>0</td><td>0</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>3347</td></tr>
<tr><td>1971</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>55</td><td>19</td><td>11</td><td>10</td><td>6</td><td>6</td><td>5</td><td>3</td><td>0</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>3344</td></tr>
<tr><td>1972</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>67</td><td>21</td><td>14</td><td>6</td><td>7</td><td>6</td><td>1</td><td>0</td><td>2</td><td>2</td><td>0</td><td>1</td><td>0</td><td>3618</td></tr>
<tr><td>1973</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>54</td><td>26</td><td>6</td><td>8</td><td>2</td><td>4</td><td>2</td><td>1</td><td>0</td><td>1</td><td>1</td><td>1</td><td>3033</td></tr>
<tr><td>1974</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>42</td><td>17</td><td>5</td><td>9</td><td>3</td><td>1</td><td>1</td><td>3</td><td>0</td><td>0</td><td>1</td><td>2729</td></tr>
<tr><td>1975</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>57</td><td>12</td><td>8</td><td>4</td><td>2</td><td>5</td><td>0</td><td>0</td><td>1</td><td>0</td><td>3077</td></tr>
<tr><td>1976</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>58</td><td>15</td><td>10</td><td>5</td><td>8</td><td>2</td><td>3</td><td>2</td><td>0</td><td>3038</td></tr>
<tr><td>1977</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>64</td><td>13</td><td>7</td><td>7</td><td>4</td><td>3</td><td>1</td><td>1</td><td>3435</td></tr>
<tr><td>1978</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>60</td><td>21</td><td>8</td><td>10</td><td>9</td><td>1</td><td>4</td><td>3533</td></tr>
<tr><td>1979</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>62</td><td>15</td><td>11</td><td>4</td><td>9</td><td>3</td><td>3814</td></tr>
<tr><td>1980</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>54</td><td>15</td><td>8</td><td>9</td><td>3</td><td>3314</td></tr>
<tr><td>1981</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>57</td><td>18</td><td>8</td><td>5</td><td>3422</td></tr>
<tr><td>1982</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>41</td><td>14</td><td>14</td><td>2858</td></tr>
<tr><td>1983</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>70</td><td>24</td><td>4056</td></tr>
  </tbody>
</table>
</div>
</details>

<details>
<summary>Birds ringed as pulli(comb)</summary>
<div style="text-align: center; margin: 1em 0;">
<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
  <thead>
    <tr>
      <th colspan="22">Birds ringed as pulli(comb)</th>
    </tr>
    <tr>
      <th rowspan="2">Year of ringing</th>
      <th colspan="20">Year recovered</th>
      <th rowspan="2">Total ringed</th>
    </tr>
    <tr>
      <th>1</th><th>2</th><th>3</th><th>4</th><th>5</th>
      <th>6</th><th>7</th><th>8</th><th>9</th><th>10</th>
      <th>11</th><th>12</th><th>13</th><th>14</th><th>15</th>
      <th>16</th><th>17</th><th>18</th><th>19</th><th>20</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>1964</td><td>67</td><td>10</td><td>2</td><td>8</td><td>5</td><td>3</td><td>1</td><td>0</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>2391</td></tr>
<tr><td>1965</td><td>0</td><td>104</td><td>14</td><td>18</td><td>7</td><td>12</td><td>2</td><td>1</td><td>3</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>3420</td></tr>
<tr><td>1966</td><td>0</td><td>0</td><td>107</td><td>20</td><td>11</td><td>4</td><td>4</td><td>2</td><td>0</td><td>2</td><td>1</td><td>0</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4366</td></tr>
<tr><td>1967</td><td>0</td><td>0</td><td>0</td><td>89</td><td>12</td><td>4</td><td>7</td><td>4</td><td>2</td><td>2</td><td>1</td><td>4</td><td>1</td><td>2</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4187</td></tr>
<tr><td>1968</td><td>0</td><td>0</td><td>0</td><td>0</td><td>131</td><td>10</td><td>8</td><td>8</td><td>6</td><td>3</td><td>6</td><td>1</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>4172</td></tr>
<tr><td>1969</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>108</td><td>10</td><td>11</td><td>10</td><td>4</td><td>1</td><td>3</td><td>1</td><td>3</td><td>1</td><td>0</td><td>0</td><td>1</td><td>0</td><td>0</td><td>4364</td></tr>
<tr><td>1970</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>73</td><td>8</td><td>5</td><td>4</td><td>4</td><td>3</td><td>2</td><td>0</td><td>0</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>3347</td></tr>
<tr><td>1971</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>74</td><td>11</td><td>10</td><td>6</td><td>6</td><td>5</td><td>3</td><td>0</td><td>1</td><td>1</td><td>0</td><td>0</td><td>0</td><td>3344</td></tr>
<tr><td>1972</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>88</td><td>14</td><td>6</td><td>7</td><td>6</td><td>1</td><td>0</td><td>2</td><td>2</td><td>0</td><td>1</td><td>0</td><td>3618</td></tr>
<tr><td>1973</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>80</td><td>6</td><td>8</td><td>2</td><td>4</td><td>2</td><td>1</td><td>0</td><td>1</td><td>1</td><td>1</td><td>3033</td></tr>
<tr><td>1974</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>59</td><td>5</td><td>9</td><td>3</td><td>1</td><td>1</td><td>3</td><td>0</td><td>0</td><td>1</td><td>2729</td></tr>
<tr><td>1975</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>69</td><td>8</td><td>4</td><td>2</td><td>5</td><td>0</td><td>0</td><td>1</td><td>0</td><td>3077</td></tr>
<tr><td>1976</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>73</td><td>10</td><td>5</td><td>8</td><td>2</td><td>3</td><td>2</td><td>0</td><td>3038</td></tr>
<tr><td>1977</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>77</td><td>7</td><td>7</td><td>4</td><td>3</td><td>1</td><td>1</td><td>3435</td></tr>
<tr><td>1978</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>81</td><td>8</td><td>10</td><td>9</td><td>1</td><td>4</td><td>3533</td></tr>
<tr><td>1979</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>77</td><td>11</td><td>4</td><td>9</td><td>3</td><td>3814</td></tr>
<tr><td>1980</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>69</td><td>8</td><td>9</td><td>3</td><td>3314</td></tr>
<tr><td>1981</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>75</td><td>8</td><td>5</td><td>3422</td></tr>
<tr><td>1982</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>55</td><td>14</td><td>2858</td></tr>
<tr><td>1983</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td>94</td><td>4056</td></tr>
  </tbody>
</table>
</div>
</details>

### GLM estimates

While MLEs can be used, it is preferable to use a GLM, as it allows for the inclusion of covariates and ensures parameters are bounded (with the right link function). I use a logit link. 

The following plots show the estimates and 95% confidence intervals for both models.

<div style="text-align: center;">
  <div class = "row">
      <img src="/assets/images/f0_GLM_comparison.png" width="400">
      <img src="/assets/images/S0_GLM_comparison.png" width="400">
  </div>
</div>

Clearly the models produce very similar estimates for adults. We see a general decline in reported exploitation rates for adult blackbirds over time, reflecting trends in reporting behaviour (https://www.researchgate.net/publication/254310554_Declining_rates_of_ring_recovery_in_British_birds). Survival rates fluctuate around 70%.

<div style="text-align: center;">
  <div class = "row">
      <img src="/assets/images/f1_GLM_comparison.png" width="400">
      <img src="/assets/images/S1_GLM_comparison.png" width="400">
  </div>
</div>

Similarly for juveniles, the values are very similar between models. We also see a decline in reported exploitation rates of juveniles. Survival rates appear to increase over the duration of the study, from around 50% to 75%. 

<div style="text-align: center;">
  <div class = "row">
      <img src="/assets/images/f2_GLM_comparison_exLast.png" width="400">
      <img src="/assets/images/S2_GLM_comparison.png" width="400">
  </div>
</div>

Post-fledging estimates exhibit the greatest difference between models. As expected, the more informative data produces estimates with less uncertainty. Reported exploitation rates show a more gradual decline. Survival rate stay fairly constant around 45%, with a possible increase towards the end of the study; there is more uncertainty for these estimates however. The final $M_{\text{comb}}$ estimate for $f''$ was removed due to uncertainty affecting readability. The plot is below

<div style="text-align: center;">
  <img src="/assets/images/f2_GLM_comparison.png" width="400">
</div>

### Bayesian modelling

While estimating parameters using a GLM is standard and is computationally cheap, it does not allow us to incorporate any prior beliefs about the system we are modelling. For this section, I will only consider the model with separate counts for the post-fledging period, as it is more informative. I present results from 2 random-walk models. These models use the same underlying multinomial framework, except they estimate $S$ and $r$, from which $f$ is estimated.

#### RW1

The priors for the variation in the random walk are given by

$${::nomarkdown}
\begin{aligned}
\sigma_{S^{\text{(age)}}} &\sim \text{N}^{+}\left(0, 1\right) \\
\sigma_{r^{\text{(age)}}} &\sim \text{N}^{+}\left(0, 1\right)
\end{aligned}
{:/}$$

The priors for the first parameter are given by
		
$${::nomarkdown}
\begin{aligned}
\text{logit}\left(S^{\text{(age)}}_{1}\right) &\sim \text{N}(0, 1) \\
\text{logit}\left(r^{\text{(age)}}_{1}\right) &\sim \text{N}(0, 1)
\end{aligned}
{:/}$$

The priors for survival and recovery rate parameters when $t > 1$ are given by

$${::nomarkdown}
\begin{aligned}
\text{logit}\left(S^{\text{(age)}}_{t}\right) - \text{logit}\left(S^{\text{(age)}}_{t-1}\right) &\sim \text{N}\left(0, \sigma_{S^{\text{(age)}}}^{2}\right) \\
\text{logit}\left(r^{\text{(age)}}_{t}\right) - \text{logit}\left(r^{\text{(age)}}_{t-1}\right) &\sim \text{N}\left(0, \sigma_{r^{\text{(age)}}}^{2}\right)
\end{aligned}
{:/}$$

These can be written as

$${::nomarkdown}
\begin{aligned}
\text{logit}\left(S^{\text{(age)}}_{t}\right) &\sim \text{N}\left(\text{logit}\left(S^{\text{(age)}}_{t-1}\right), \sigma_{S^{\text{(age)}}}^{2}\right) \\
\text{logit}\left(r^{\text{(age)}}_{t}\right) &\sim \text{N}\left(\text{logit}\left(r^{\text{(age)}}_{t-1}\right), \sigma_{r^{\text{(age)}}}^{2}\right)
\end{aligned}
{:/}$$

Fitting this model yields the following estimates for $f$, $r$, and $S$

<div style="text-align: center;">
  <div class = "row">
      <img src="/assets/images/f0_RW1.png" width="400">
      <img src="/assets/images/r0_RW1.png" width="400">
      <img src="/assets/images/S0_RW1.png" width="400">
  </div>
</div>

<!--
The estimates imply that, under the assumption that first differences follow a $N\left(0, \sigma_{S}^{2}\right)$ distribution, the adult recovery rate (and harvest rate) declined over the period of the scheme. We also see that the adult survival rate increased over the first 10 years from approximately 70% to 75%, after which it declined to 67%, then returned to 70%. This could suggest that the survival rate of blackbirds oscillate, potentially due to multi-year climate patterns. This does, however, rely on the assumption that the priors are realistic. 

For juveniles, we find
-->

<div style="text-align: center;">
  <div class = "row">
      <img src="/assets/images/f1_RW1.png" width="400">
      <img src="/assets/images/r1_RW1.png" width="400">
      <img src="/assets/images/S1_RW1.png" width="400">
  </div>
</div>

<!--
Unlike adults, we do not see a steady decline in recovery rates; they begin to decline from 5.5% in the first year to 4% in the 5th year, but then increase back up to 5.5% in the 10th year. After this, there is a decline to around 4%. This model suggests the survival rate of juveniles steadily increase from 55% to 70%. This is also unlike the adults. 

A possible explanation for this is that the natural predation on blackbirds decreased. For example, if species $A$ predated on blackbirds, and typically targeted the young as they were weak, but experienced an event causing their population to decline, then the juvenile survival rate might increase.

Finally, the rates for birds recovered during the post-fledging period are
-->

<div style="text-align: center;">
  <div class = "row">
      <img src="/assets/images/f2_RW1.png" width="400">
      <img src="/assets/images/r2_RW1.png" width="400">
      <img src="/assets/images/S2_RW1.png" width="400">
  </div>
</div>

#### RW2

The priors for the variation in the random walk are given by

$${::nomarkdown}
\begin{aligned}
\sigma_{S^{\text{(age)}}} &\sim \text{N}^{+}\left(0, 1\right) \\
\sigma_{r^{\text{(age)}}} &\sim \text{N}^{+}\left(0, 1\right)
\end{aligned}
{:/}$$

The priors for the first 2 parameters are given by
		
$${::nomarkdown}
\begin{aligned}
\text{logit}\left(S^{\text{(age)}}_{1}\right) &\sim \text{N}(0, 1) \\
\text{logit}\left(S^{\text{(age)}}_{2}\right) &\sim \text{N}\left(\text{logit}\left(S^{\text{(age)}}_{1}\right), 1\right) \\
\text{logit}\left(r^{\text{(age)}}_{1}\right) &\sim \text{N}(0, 1) \\
\text{logit}\left(r^{\text{(age)}}_{2}\right) &\sim \text{N}\left(\text{logit}\left(r^{\text{(age)}}_{1}\right), 1\right)
\end{aligned}
{:/}$$

The priors for survival and recovery rate parameters when $t > 2$ are given by

$${::nomarkdown}
\begin{aligned}
\left(\text{logit}\left(S^{\text{(age)}}_{t}\right) - \text{logit}\left(S^{\text{(age)}}_{t-1}\right)\right) - \left(\text{logit}\left(S^{\text{(age)}}_{t-1}\right) - \text{logit}\left(S^{\text{(age)}}_{t-2}\right)\right) &\sim \text{N}\left(0,\sigma_{S^{\text{(age)}}}^{2}\right) \\
\left(\text{logit}\left(r^{\text{(age)}}_{t}\right) - \text{logit}\left(r^{\text{(age)}}_{t-1}\right)\right) - \left(\text{logit}\left(r^{\text{(age)}}_{t-1}\right) - \text{logit}\left(r^{\text{(age)}}_{t-2}\right)\right) &\sim \text{N}\left(0,\sigma_{r^{\text{(age)}}}^{2}\right) \end{aligned}
{:/}$$

These can be written as

$${::nomarkdown}
\begin{aligned}
\text{logit}\left(S^{\text{(age)}}_{t}\right) &\sim \text{N}\left(2\cdot\text{logit}\left(S^{\text{(age)}}_{t-1}\right) - \text{logit}\left(S^{\text{(age)}}_{t-2}\right), \sigma_{S^{\text{(age)}}}^{2}\right) \\
\text{logit}\left(r^{\text{(age)}}_{t}\right) &\sim \text{N}\left(2\cdot\text{logit}\left(r^{\text{(age)}}_{t-1}\right) - \text{logit}\left(r^{\text{(age)}}_{t-2}\right), \sigma_{r^{\text{(age)}}}^{2}\right)
\end{aligned}
{:/}$$

### Data frames

Data frames containing the estimates, standard errors, and confidence interval bounds are below

<details>
<summary>M<sub>sep</sub> (GLM)</summary>
<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
 <tr><th>Time</th><th>Param</th><th>Age</th><th>Estim</th><th>StdErr</th><th>Lower</th><th>Upper</th></tr>
 <tr><td>1</td><td>f</td><td>0</td><td>0.020654727</td><td>0.003270655</td><td>0.015129707</td><td>0.028139709</td></tr>
 <tr><td>1</td><td>f</td><td>1</td><td>0.025653447</td><td>0.002332144</td><td>0.021458062</td><td>0.030643409</td></tr>
 <tr><td>1</td><td>f</td><td>2</td><td>0.021759267</td><td>0.002983725</td><td>0.016618871</td><td>0.028443653</td></tr>
 <tr><td>1</td><td>S</td><td>0</td><td>0.624955631</td><td>0.09376761</td><td>0.432054471</td><td>0.784949497</td></tr>
 <tr><td>1</td><td>S</td><td>1</td><td>0.510775875</td><td>0.062849467</td><td>0.38939438</td><td>0.630899816</td></tr>
 <tr><td>1</td><td>S</td><td>2</td><td>0.352778052</td><td>0.056942224</td><td>0.250554857</td><td>0.470522948</td></tr>
 <tr><td>2</td><td>f</td><td>0</td><td>0.018213479</td><td>0.002110217</td><td>0.014507002</td><td>0.022844994</td></tr>
 <tr><td>2</td><td>f</td><td>1</td><td>0.019592379</td><td>0.001891126</td><td>0.016209833</td><td>0.023663791</td></tr>
 <tr><td>2</td><td>f</td><td>2</td><td>0.021660073</td><td>0.002489238</td><td>0.017282583</td><td>0.027115739</td></tr>
 <tr><td>2</td><td>S</td><td>0</td><td>0.80437273</td><td>0.10773568</td><td>0.517973398</td><td>0.940238875</td></tr>
 <tr><td>2</td><td>S</td><td>1</td><td>0.627863255</td><td>0.079247071</td><td>0.464631824</td><td>0.766352988</td></tr>
 <tr><td>2</td><td>S</td><td>2</td><td>0.545908836</td><td>0.067887495</td><td>0.41274986</td><td>0.672808946</td></tr>
 <tr><td>3</td><td>f</td><td>0</td><td>0.011876461</td><td>0.001433839</td><td>0.009370974</td><td>0.015041662</td></tr>
 <tr><td>3</td><td>f</td><td>1</td><td>0.017352387</td><td>0.001875767</td><td>0.014034055</td><td>0.021438274</td></tr>
 <tr><td>3</td><td>f</td><td>2</td><td>0.017865576</td><td>0.002004722</td><td>0.014332526</td><td>0.022249883</td></tr>
 <tr><td>3</td><td>S</td><td>0</td><td>0.711539438</td><td>0.091918623</td><td>0.506278609</td><td>0.85577454</td></tr>
 <tr><td>3</td><td>S</td><td>1</td><td>0.539856398</td><td>0.067216018</td><td>0.408401147</td><td>0.665990954</td></tr>
 <tr><td>3</td><td>S</td><td>2</td><td>0.401224927</td><td>0.055327078</td><td>0.299067203</td><td>0.512751243</td></tr>
 <tr><td>4</td><td>f</td><td>0</td><td>0.015239982</td><td>0.001526295</td><td>0.012520247</td><td>0.018539424</td></tr>
 <tr><td>4</td><td>f</td><td>1</td><td>0.016924478</td><td>0.001715281</td><td>0.01387097</td><td>0.020636109</td></tr>
 <tr><td>4</td><td>f</td><td>2</td><td>0.015988037</td><td>0.001938395</td><td>0.012601108</td><td>0.02026662</td></tr>
 <tr><td>4</td><td>S</td><td>0</td><td>0.707161638</td><td>0.087211534</td><td>0.514046954</td><td>0.846456427</td></tr>
 <tr><td>4</td><td>S</td><td>1</td><td>0.573307102</td><td>0.069898101</td><td>0.434212975</td><td>0.701697965</td></tr>
 <tr><td>4</td><td>S</td><td>2</td><td>0.348027304</td><td>0.050253542</td><td>0.256963333</td><td>0.451742806</td></tr>
 <tr><td>5</td><td>f</td><td>0</td><td>0.013275103</td><td>0.001380069</td><td>0.010825115</td><td>0.016270462</td></tr>
 <tr><td>5</td><td>f</td><td>1</td><td>0.014762658</td><td>0.001521134</td><td>0.01205956</td><td>0.018060569</td></tr>
 <tr><td>5</td><td>f</td><td>2</td><td>0.024208653</td><td>0.002379542</td><td>0.019957858</td><td>0.029337717</td></tr>
 <tr><td>5</td><td>S</td><td>0</td><td>0.510404213</td><td>0.059582872</td><td>0.395151397</td><td>0.624561453</td></tr>
 <tr><td>5</td><td>S</td><td>1</td><td>0.414015724</td><td>0.045784631</td><td>0.327992372</td><td>0.505625096</td></tr>
 <tr><td>5</td><td>S</td><td>2</td><td>0.450516715</td><td>0.06033346</td><td>0.33709292</td><td>0.56932798</td></tr>
 <tr><td>6</td><td>f</td><td>0</td><td>0.019606574</td><td>0.001736303</td><td>0.016477701</td><td>0.02331549</td></tr>
 <tr><td>6</td><td>f</td><td>1</td><td>0.01556867</td><td>0.001613155</td><td>0.01270339</td><td>0.01906774</td></tr>
 <tr><td>6</td><td>f</td><td>2</td><td>0.018548845</td><td>0.00204243</td><td>0.014942053</td><td>0.023005931</td></tr>
 <tr><td>6</td><td>S</td><td>0</td><td>0.833379813</td><td>0.092830346</td><td>0.574310115</td><td>0.948830513</td></tr>
 <tr><td>6</td><td>S</td><td>1</td><td>0.563928202</td><td>0.064588712</td><td>0.435936343</td><td>0.683934128</td></tr>
 <tr><td>6</td><td>S</td><td>2</td><td>0.388634111</td><td>0.052493946</td><td>0.291916861</td><td>0.494994634</td></tr>
 <tr><td>7</td><td>f</td><td>0</td><td>0.013946372</td><td>0.001337604</td><td>0.01155363</td><td>0.016826212</td></tr>
 <tr><td>7</td><td>f</td><td>1</td><td>0.014694948</td><td>0.001747253</td><td>0.011635735</td><td>0.018543384</td></tr>
 <tr><td>7</td><td>f</td><td>2</td><td>0.01735096</td><td>0.002257012</td><td>0.01343902</td><td>0.022375794</td></tr>
 <tr><td>7</td><td>S</td><td>0</td><td>0.806496566</td><td>0.094284982</td><td>0.560515031</td><td>0.931601625</td></tr>
 <tr><td>7</td><td>S</td><td>1</td><td>0.618051988</td><td>0.076295583</td><td>0.462027718</td><td>0.75301514</td></tr>
 <tr><td>7</td><td>S</td><td>2</td><td>0.316390471</td><td>0.053602291</td><td>0.221636619</td><td>0.429310403</td></tr>
 <tr><td>8</td><td>f</td><td>0</td><td>0.011605432</td><td>0.0011439</td><td>0.009564692</td><td>0.014075401</td></tr>
 <tr><td>8</td><td>f</td><td>1</td><td>0.013740401</td><td>0.00171319</td><td>0.010757229</td><td>0.017536196</td></tr>
 <tr><td>8</td><td>f</td><td>2</td><td>0.01645331</td><td>0.002199791</td><td>0.012653688</td><td>0.021369182</td></tr>
 <tr><td>8</td><td>S</td><td>0</td><td>0.780007501</td><td>0.091626595</td><td>0.554565228</td><td>0.909889508</td></tr>
 <tr><td>8</td><td>S</td><td>1</td><td>0.692595617</td><td>0.086285635</td><td>0.50448619</td><td>0.832941476</td></tr>
 <tr><td>8</td><td>S</td><td>2</td><td>0.45727062</td><td>0.068153236</td><td>0.329689934</td><td>0.590713784</td></tr>
 <tr><td>9</td><td>f</td><td>0</td><td>0.012507136</td><td>0.001185426</td><td>0.010384609</td><td>0.015056888</td></tr>
 <tr><td>9</td><td>f</td><td>1</td><td>0.017657292</td><td>0.001925792</td><td>0.014253438</td><td>0.021855995</td></tr>
 <tr><td>9</td><td>f</td><td>2</td><td>0.018517843</td><td>0.00224129</td><td>0.01459992</td><td>0.023462117</td></tr>
 <tr><td>9</td><td>S</td><td>0</td><td>0.707732349</td><td>0.083967153</td><td>0.522174856</td><td>0.842909077</td></tr>
 <tr><td>9</td><td>S</td><td>1</td><td>0.648208025</td><td>0.081526726</td><td>0.477623341</td><td>0.787832553</td></tr>
 <tr><td>9</td><td>S</td><td>2</td><td>0.397399713</td><td>0.059207182</td><td>0.288865475</td><td>0.517062755</td></tr>
 <tr><td>10</td><td>f</td><td>0</td><td>0.011528492</td><td>0.001111907</td><td>0.009540885</td><td>0.013924346</td></tr>
 <tr><td>10</td><td>f</td><td>1</td><td>0.020940171</td><td>0.002104241</td><td>0.017189918</td><td>0.025487384</td></tr>
 <tr><td>10</td><td>f</td><td>2</td><td>0.017785629</td><td>0.002399913</td><td>0.013644485</td><td>0.02315412</td></tr>
 <tr><td>10</td><td>S</td><td>0</td><td>0.760498401</td><td>0.084253834</td><td>0.561872565</td><td>0.887160812</td></tr>
 <tr><td>10</td><td>S</td><td>1</td><td>0.865681526</td><td>0.09776731</td><td>0.553620176</td><td>0.971007534</td></tr>
 <tr><td>10</td><td>S</td><td>2</td><td>0.342263256</td><td>0.0525747</td><td>0.247690787</td><td>0.45128432</td></tr>
 <tr><td>11</td><td>f</td><td>0</td><td>0.010467134</td><td>0.000950549</td><td>0.008759066</td><td>0.012504084</td></tr>
 <tr><td>11</td><td>f</td><td>1</td><td>0.019442836</td><td>0.002253051</td><td>0.015485079</td><td>0.024387081</td></tr>
 <tr><td>11</td><td>f</td><td>2</td><td>0.015372908</td><td>0.002355146</td><td>0.011378133</td><td>0.020740793</td></tr>
 <tr><td>11</td><td>S</td><td>0</td><td>0.98535085</td><td>0.060036294</td><td>0.019013102</td><td>0.999995716</td></tr>
 <tr><td>11</td><td>S</td><td>1</td><td>0.832296904</td><td>0.113042017</td><td>0.503657386</td><td>0.960431845</td></tr>
 <tr><td>11</td><td>S</td><td>2</td><td>0.373961838</td><td>0.066986424</td><td>0.254251153</td><td>0.511385821</td></tr>
 <tr><td>12</td><td>f</td><td>0</td><td>0.007838441</td><td>0.000682081</td><td>0.006608634</td><td>0.009294962</td></tr>
 <tr><td>12</td><td>f</td><td>1</td><td>0.017123125</td><td>0.001944597</td><td>0.013700601</td><td>0.021382089</td></tr>
 <tr><td>12</td><td>f</td><td>2</td><td>0.018531522</td><td>0.002431255</td><td>0.0143214</td><td>0.02394924</td></tr>
 <tr><td>12</td><td>S</td><td>0</td><td>0.595596235</td><td>0.070907366</td><td>0.452677869</td><td>0.72395296</td></tr>
 <tr><td>12</td><td>S</td><td>1</td><td>0.811010992</td><td>0.11299933</td><td>0.502897485</td><td>0.947925249</td></tr>
 <tr><td>12</td><td>S</td><td>2</td><td>0.269005893</td><td>0.051868073</td><td>0.179953852</td><td>0.381618526</td></tr>
 <tr><td>13</td><td>f</td><td>0</td><td>0.009851807</td><td>0.001021195</td><td>0.008038943</td><td>0.012068516</td></tr>
 <tr><td>13</td><td>f</td><td>1</td><td>0.014279455</td><td>0.001646456</td><td>0.011387146</td><td>0.017893108</td></tr>
 <tr><td>13</td><td>f</td><td>2</td><td>0.019119118</td><td>0.002484554</td><td>0.014811466</td><td>0.024648231</td></tr>
 <tr><td>13</td><td>S</td><td>0</td><td>0.611310993</td><td>0.081739294</td><td>0.444868542</td><td>0.755299371</td></tr>
 <tr><td>13</td><td>S</td><td>1</td><td>0.730904161</td><td>0.099530655</td><td>0.501841455</td><td>0.879855621</td></tr>
 <tr><td>13</td><td>S</td><td>2</td><td>0.432598551</td><td>0.072598007</td><td>0.299231611</td><td>0.576505833</td></tr>
 <tr><td>14</td><td>f</td><td>0</td><td>0.009805907</td><td>0.001037971</td><td>0.007967068</td><td>0.012063999</td></tr>
 <tr><td>14</td><td>f</td><td>1</td><td>0.015603755</td><td>0.001661632</td><td>0.012660285</td><td>0.019218249</td></tr>
 <tr><td>14</td><td>f</td><td>2</td><td>0.01867445</td><td>0.002309719</td><td>0.014646766</td><td>0.023782966</td></tr>
 <tr><td>14</td><td>S</td><td>0</td><td>0.688220615</td><td>0.094455718</td><td>0.482261291</td><td>0.839513381</td></tr>
 <tr><td>14</td><td>S</td><td>1</td><td>0.722862169</td><td>0.100102851</td><td>0.494831098</td><td>0.87414145</td></tr>
 <tr><td>14</td><td>S</td><td>2</td><td>0.309452535</td><td>0.056301832</td><td>0.210970452</td><td>0.428916223</td></tr>
 <tr><td>15</td><td>f</td><td>0</td><td>0.009408594</td><td>0.001029292</td><td>0.007591259</td><td>0.011655886</td></tr>
 <tr><td>15</td><td>f</td><td>1</td><td>0.015133192</td><td>0.001665375</td><td>0.012193015</td><td>0.01876888</td></tr>
 <tr><td>15</td><td>f</td><td>2</td><td>0.017004444</td><td>0.002175121</td><td>0.013226998</td><td>0.021836802</td></tr>
 <tr><td>15</td><td>S</td><td>0</td><td>0.543782447</td><td>0.074309746</td><td>0.398549983</td><td>0.681933019</td></tr>
 <tr><td>15</td><td>S</td><td>1</td><td>0.567531692</td><td>0.079201393</td><td>0.410792767</td><td>0.711825005</td></tr>
 <tr><td>15</td><td>S</td><td>2</td><td>0.471265743</td><td>0.074775804</td><td>0.331092349</td><td>0.616122201</td></tr>
 <tr><td>16</td><td>f</td><td>0</td><td>0.011355034</td><td>0.001182788</td><td>0.009256031</td><td>0.013923342</td></tr>
 <tr><td>16</td><td>f</td><td>1</td><td>0.014101964</td><td>0.001566772</td><td>0.011338863</td><td>0.017526454</td></tr>
 <tr><td>16</td><td>f</td><td>2</td><td>0.016299782</td><td>0.00205037</td><td>0.012732219</td><td>0.020845869</td></tr>
 <tr><td>16</td><td>S</td><td>0</td><td>0.642421222</td><td>0.083754246</td><td>0.467863084</td><td>0.785919477</td></tr>
 <tr><td>16</td><td>S</td><td>1</td><td>0.571802553</td><td>0.077892851</td><td>0.417187562</td><td>0.713562193</td></tr>
 <tr><td>16</td><td>S</td><td>2</td><td>0.364441096</td><td>0.063086743</td><td>0.251622095</td><td>0.494425245</td></tr>
 <tr><td>17</td><td>f</td><td>0</td><td>0.011671297</td><td>0.001161708</td><td>0.009600664</td><td>0.014182122</td></tr>
 <tr><td>17</td><td>f</td><td>1</td><td>0.01087188</td><td>0.001310926</td><td>0.008581077</td><td>0.013765744</td></tr>
 <tr><td>17</td><td>f</td><td>2</td><td>0.016293178</td><td>0.002199153</td><td>0.012499209</td><td>0.021214015</td></tr>
 <tr><td>17</td><td>S</td><td>0</td><td>0.773440497</td><td>0.100664104</td><td>0.525449211</td><td>0.913235589</td></tr>
 <tr><td>17</td><td>S</td><td>1</td><td>0.61690007</td><td>0.088280023</td><td>0.436414991</td><td>0.770041745</td></tr>
 <tr><td>17</td><td>S</td><td>2</td><td>0.44023602</td><td>0.083088459</td><td>0.28883285</td><td>0.603638988</td></tr>
 <tr><td>18</td><td>f</td><td>0</td><td>0.010228615</td><td>0.001052026</td><td>0.008359545</td><td>0.012510308</td></tr>
 <tr><td>18</td><td>f</td><td>1</td><td>0.011624251</td><td>0.001383774</td><td>0.009202492</td><td>0.014673889</td></tr>
 <tr><td>18</td><td>f</td><td>2</td><td>0.016672679</td><td>0.002188823</td><td>0.012883428</td><td>0.021552084</td></tr>
 <tr><td>18</td><td>S</td><td>0</td><td>0.961180287</td><td>0.125700593</td><td>0.032493283</td><td>0.999945221</td></tr>
 <tr><td>18</td><td>S</td><td>1</td><td>0.829941145</td><td>0.146588062</td><td>0.389250613</td><td>0.973938366</td></tr>
 <tr><td>18</td><td>S</td><td>2</td><td>0.427286981</td><td>0.085958814</td><td>0.27261281</td><td>0.597617708</td></tr>
 <tr><td>19</td><td>f</td><td>0</td><td>0.006701327</td><td>0.000856235</td><td>0.005215732</td><td>0.008606403</td></tr>
 <tr><td>19</td><td>f</td><td>1</td><td>0.010328084</td><td>0.001301482</td><td>0.00806542</td><td>0.013217055</td></tr>
 <tr><td>19</td><td>f</td><td>2</td><td>0.014343665</td><td>0.00222415</td><td>0.01057802</td><td>0.019423518</td></tr>
 <tr><td>19</td><td>S</td><td>0</td><td>0.552393474</td><td>0.114628879</td><td>0.332184409</td><td>0.753805761</td></tr>
 <tr><td>19</td><td>S</td><td>1</td><td>0.808358064</td><td>0.178436313</td><td>0.306143088</td><td>0.975801439</td></tr>
 <tr><td>19</td><td>S</td><td>2</td><td>0.561548602</td><td>0.120086994</td><td>0.329926308</td><td>0.769132108</td></tr>
 <tr><td>20</td><td>f</td><td>0</td><td>0.008781213</td><td>0.001389404</td><td>0.006437297</td><td>0.011968302</td></tr>
 <tr><td>20</td><td>f</td><td>1</td><td>0.017911743</td><td>0.001600518</td><td>0.015030099</td><td>0.021333902</td></tr>
 <tr><td>20</td><td>f</td><td>2</td><td>0.017351767</td><td>0.002056414</td><td>0.013749003</td><td>0.02187765</td></tr>
 <tr><td>20</td><td>S</td><td>0</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
 <tr><td>20</td><td>S</td><td>1</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
 <tr><td>20</td><td>S</td><td>2</td><td>0.997980667</td><td>0.009163383</td><td>0.062434436</td><td>0.999999727</td></tr>
</table>
</details>

<details>
<summary>M<sub>comb</sub> (GLM)</summary>
<table style="margin: 0 auto; border-collapse: collapse; width: auto;">
 <tr><th>Time</th><th>Param</th><th>Age</th><th>Estim</th><th>StdErr</th><th>Lower</th><th>Upper</th></tr>
 <tr><td>1</td><td>f</td><td>0</td><td>0.020582996</td><td>0.003265001</td><td>1.51E-02</td><td>0.028057217</td></tr>
 <tr><td>1</td><td>f</td><td>1</td><td>0.027246432</td><td>0.002516883</td><td>2.27E-02</td><td>0.032638416</td></tr>
 <tr><td>1</td><td>f</td><td>2</td><td>0.014906801</td><td>0.004521316</td><td>8.21E-03</td><td>0.026924007</td></tr>
 <tr><td>1</td><td>S</td><td>0</td><td>0.625716095</td><td>0.093868837</td><td>4.32E-01</td><td>0.785747069</td></tr>
 <tr><td>1</td><td>S</td><td>1</td><td>0.480039728</td><td>0.06120146</td><td>3.63E-01</td><td>0.598856261</td></tr>
 <tr><td>1</td><td>S</td><td>2</td><td>0.480994738</td><td>0.098356309</td><td>3.00E-01</td><td>0.667338397</td></tr>
 <tr><td>2</td><td>f</td><td>0</td><td>0.018241243</td><td>0.002110666</td><td>1.45E-02</td><td>0.022873029</td></tr>
 <tr><td>2</td><td>f</td><td>1</td><td>0.021079811</td><td>0.002162712</td><td>1.72E-02</td><td>0.025763</td></tr>
 <tr><td>2</td><td>f</td><td>2</td><td>0.016696609</td><td>0.003952972</td><td>1.05E-02</td><td>0.026498871</td></tr>
 <tr><td>2</td><td>S</td><td>0</td><td>0.802702065</td><td>0.106449913</td><td>5.21E-01</td><td>0.93823649</td></tr>
 <tr><td>2</td><td>S</td><td>1</td><td>0.592449812</td><td>0.077777635</td><td>4.36E-01</td><td>0.732131439</td></tr>
 <tr><td>2</td><td>S</td><td>2</td><td>0.650815858</td><td>0.102857245</td><td>4.34E-01</td><td>0.819022531</td></tr>
 <tr><td>3</td><td>f</td><td>0</td><td>0.011905676</td><td>0.001431427</td><td>9.40E-03</td><td>0.015063962</td></tr>
 <tr><td>3</td><td>f</td><td>1</td><td>0.017716542</td><td>0.002097897</td><td>1.40E-02</td><td>0.022333024</td></tr>
 <tr><td>3</td><td>f</td><td>2</td><td>0.017058047</td><td>0.002853945</td><td>1.23E-02</td><td>0.023653329</td></tr>
 <tr><td>3</td><td>S</td><td>0</td><td>0.712311756</td><td>0.09152893</td><td>5.08E-01</td><td>0.855952267</td></tr>
 <tr><td>3</td><td>S</td><td>1</td><td>0.531919394</td><td>0.069244466</td><td>3.97E-01</td><td>0.662163298</td></tr>
 <tr><td>3</td><td>S</td><td>2</td><td>0.420545774</td><td>0.075296711</td><td>2.84E-01</td><td>0.570792658</td></tr>
 <tr><td>4</td><td>f</td><td>0</td><td>0.015250795</td><td>0.001526652</td><td>1.25E-02</td><td>0.018550847</td></tr>
 <tr><td>4</td><td>f</td><td>1</td><td>0.017468847</td><td>0.001878183</td><td>1.41E-02</td><td>0.021557576</td></tr>
 <tr><td>4</td><td>f</td><td>2</td><td>0.014610271</td><td>0.002655356</td><td>1.02E-02</td><td>0.020840244</td></tr>
 <tr><td>4</td><td>S</td><td>0</td><td>0.70705115</td><td>0.087270368</td><td>5.14E-01</td><td>0.846435743</td></tr>
 <tr><td>4</td><td>S</td><td>1</td><td>0.560399252</td><td>0.070450177</td><td>4.21E-01</td><td>0.690678025</td></tr>
 <tr><td>4</td><td>S</td><td>2</td><td>0.380773751</td><td>0.069786486</td><td>2.56E-01</td><td>0.52344419</td></tr>
 <tr><td>5</td><td>f</td><td>0</td><td>0.013286065</td><td>0.001381513</td><td>1.08E-02</td><td>0.016284623</td></tr>
 <tr><td>5</td><td>f</td><td>1</td><td>0.014324571</td><td>0.001642646</td><td>1.14E-02</td><td>0.017927579</td></tr>
 <tr><td>5</td><td>f</td><td>2</td><td>0.025375579</td><td>0.00299403</td><td>2.01E-02</td><td>0.03195388</td></tr>
 <tr><td>5</td><td>S</td><td>0</td><td>0.510890081</td><td>0.059730077</td><td>3.95E-01</td><td>0.625292371</td></tr>
 <tr><td>5</td><td>S</td><td>1</td><td>0.421209827</td><td>0.047812497</td><td>3.31E-01</td><td>0.516640402</td></tr>
 <tr><td>5</td><td>S</td><td>2</td><td>0.421364502</td><td>0.072890258</td><td>2.88E-01</td><td>0.566792928</td></tr>
 <tr><td>6</td><td>f</td><td>0</td><td>0.019609941</td><td>0.001738007</td><td>1.65E-02</td><td>0.023322757</td></tr>
 <tr><td>6</td><td>f</td><td>1</td><td>0.015484227</td><td>0.001761231</td><td>1.24E-02</td><td>0.019343148</td></tr>
 <tr><td>6</td><td>f</td><td>2</td><td>0.018813024</td><td>0.002661553</td><td>1.42E-02</td><td>0.024804618</td></tr>
 <tr><td>6</td><td>S</td><td>0</td><td>0.831695173</td><td>0.092864989</td><td>5.74E-01</td><td>0.947747505</td></tr>
 <tr><td>6</td><td>S</td><td>1</td><td>0.564593072</td><td>0.066171805</td><td>4.33E-01</td><td>0.687276257</td></tr>
 <tr><td>6</td><td>S</td><td>2</td><td>0.380944647</td><td>0.065230511</td><td>2.64E-01</td><td>0.514147331</td></tr>
 <tr><td>7</td><td>f</td><td>0</td><td>0.013977195</td><td>0.001340099</td><td>1.16E-02</td><td>0.016862302</td></tr>
 <tr><td>7</td><td>f</td><td>1</td><td>0.014836106</td><td>0.00188518</td><td>1.16E-02</td><td>0.019022216</td></tr>
 <tr><td>7</td><td>f</td><td>2</td><td>0.016974716</td><td>0.002802843</td><td>1.23E-02</td><td>0.02343779</td></tr>
 <tr><td>7</td><td>S</td><td>0</td><td>0.809491004</td><td>0.094863957</td><td>5.60E-01</td><td>0.934157526</td></tr>
 <tr><td>7</td><td>S</td><td>1</td><td>0.617057482</td><td>0.078307926</td><td>4.57E-01</td><td>0.755213174</td></tr>
 <tr><td>7</td><td>S</td><td>2</td><td>0.324357881</td><td>0.068531813</td><td>2.06E-01</td><td>0.469813917</td></tr>
 <tr><td>8</td><td>f</td><td>0</td><td>0.011585193</td><td>0.001144752</td><td>9.54E-03</td><td>0.014057614</td></tr>
 <tr><td>8</td><td>f</td><td>1</td><td>0.014268168</td><td>0.001945597</td><td>1.09E-02</td><td>0.018629192</td></tr>
 <tr><td>8</td><td>f</td><td>2</td><td>0.015153236</td><td>0.003016532</td><td>1.02E-02</td><td>0.02235505</td></tr>
 <tr><td>8</td><td>S</td><td>0</td><td>0.779572588</td><td>0.091782118</td><td>5.54E-01</td><td>0.909705593</td></tr>
 <tr><td>8</td><td>S</td><td>1</td><td>0.678621089</td><td>0.087515067</td><td>4.90E-01</td><td>0.822581155</td></tr>
 <tr><td>8</td><td>S</td><td>2</td><td>0.489331289</td><td>0.088796438</td><td>3.23E-01</td><td>0.657866238</td></tr>
 <tr><td>9</td><td>f</td><td>0</td><td>0.012493413</td><td>0.001183494</td><td>1.04E-02</td><td>0.015038886</td></tr>
 <tr><td>9</td><td>f</td><td>1</td><td>0.018760183</td><td>0.002159794</td><td>1.50E-02</td><td>0.0234967</td></tr>
 <tr><td>9</td><td>f</td><td>2</td><td>0.015572127</td><td>0.003241273</td><td>1.03E-02</td><td>0.023381321</td></tr>
 <tr><td>9</td><td>S</td><td>0</td><td>0.707860778</td><td>0.083868227</td><td>5.23E-01</td><td>0.842894339</td></tr>
 <tr><td>9</td><td>S</td><td>1</td><td>0.620918962</td><td>0.082406497</td><td>4.52E-01</td><td>0.764884921</td></tr>
 <tr><td>9</td><td>S</td><td>2</td><td>0.468000592</td><td>0.088935829</td><td>3.04E-01</td><td>0.639213395</td></tr>
 <tr><td>10</td><td>f</td><td>0</td><td>0.011512346</td><td>0.001111409</td><td>9.53E-03</td><td>0.013907351</td></tr>
 <tr><td>10</td><td>f</td><td>1</td><td>0.019905205</td><td>0.002188566</td><td>1.60E-02</td><td>0.024679565</td></tr>
 <tr><td>10</td><td>f</td><td>2</td><td>0.020817566</td><td>0.00322891</td><td>1.53E-02</td><td>0.028182914</td></tr>
 <tr><td>10</td><td>S</td><td>0</td><td>0.763984855</td><td>0.087782308</td><td>5.55E-01</td><td>0.893675772</td></tr>
 <tr><td>10</td><td>S</td><td>1</td><td>0.911148408</td><td>0.117728151</td><td>3.72E-01</td><td>0.994392251</td></tr>
 <tr><td>10</td><td>S</td><td>2</td><td>0.280269633</td><td>0.060856557</td><td>1.77E-01</td><td>0.412943248</td></tr>
 <tr><td>11</td><td>f</td><td>0</td><td>0.010400722</td><td>0.00099445</td><td>8.62E-03</td><td>0.012541978</td></tr>
 <tr><td>11</td><td>f</td><td>1</td><td>0.020310057</td><td>0.002455534</td><td>1.60E-02</td><td>0.02572479</td></tr>
 <tr><td>11</td><td>f</td><td>2</td><td>0.012409904</td><td>0.003765849</td><td>6.83E-03</td><td>0.022433148</td></tr>
 <tr><td>11</td><td>S</td><td>0</td><td>0.980161147</td><td>0.072619756</td><td>3.17E-02</td><td>0.999986595</td></tr>
 <tr><td>11</td><td>S</td><td>1</td><td>0.789755901</td><td>0.114850767</td><td>4.92E-01</td><td>0.935787162</td></tr>
 <tr><td>11</td><td>S</td><td>2</td><td>0.453565959</td><td>0.110065284</td><td>2.58E-01</td><td>0.664663376</td></tr>
 <tr><td>12</td><td>f</td><td>0</td><td>0.007834395</td><td>0.000704741</td><td>6.57E-03</td><td>0.009343722</td></tr>
 <tr><td>12</td><td>f</td><td>1</td><td>0.017673477</td><td>0.002078666</td><td>1.40E-02</td><td>0.022244042</td></tr>
 <tr><td>12</td><td>f</td><td>2</td><td>0.01694566</td><td>0.003074915</td><td>1.19E-02</td><td>0.024153749</td></tr>
 <tr><td>12</td><td>S</td><td>0</td><td>0.593290474</td><td>0.0718313</td><td>4.49E-01</td><td>0.723332839</td></tr>
 <tr><td>12</td><td>S</td><td>1</td><td>0.786305049</td><td>0.111672862</td><td>5.00E-01</td><td>0.931209411</td></tr>
 <tr><td>12</td><td>S</td><td>2</td><td>0.307844975</td><td>0.076386315</td><td>1.81E-01</td><td>0.473133834</td></tr>
 <tr><td>13</td><td>f</td><td>0</td><td>0.009894397</td><td>0.001021526</td><td>8.08E-03</td><td>0.012110899</td></tr>
 <tr><td>13</td><td>f</td><td>1</td><td>0.015124011</td><td>0.001819485</td><td>1.19E-02</td><td>0.019136898</td></tr>
 <tr><td>13</td><td>f</td><td>2</td><td>0.016182179</td><td>0.003393604</td><td>1.07E-02</td><td>0.024369942</td></tr>
 <tr><td>13</td><td>S</td><td>0</td><td>0.614162216</td><td>0.082280559</td><td>4.46E-01</td><td>0.758669805</td></tr>
 <tr><td>13</td><td>S</td><td>1</td><td>0.70063009</td><td>0.099885687</td><td>4.79E-01</td><td>0.856151723</td></tr>
 <tr><td>13</td><td>S</td><td>2</td><td>0.519669616</td><td>0.10978194</td><td>3.14E-01</td><td>0.719249826</td></tr>
 <tr><td>14</td><td>f</td><td>0</td><td>0.009794613</td><td>0.001040679</td><td>7.95E-03</td><td>0.0120595</td></tr>
 <tr><td>14</td><td>f</td><td>1</td><td>0.016311647</td><td>0.00178588</td><td>1.32E-02</td><td>0.020207621</td></tr>
 <tr><td>14</td><td>f</td><td>2</td><td>0.01620106</td><td>0.003005617</td><td>1.13E-02</td><td>0.023276984</td></tr>
 <tr><td>14</td><td>S</td><td>0</td><td>0.687144635</td><td>0.09489939</td><td>4.80E-01</td><td>0.839164703</td></tr>
 <tr><td>14</td><td>S</td><td>1</td><td>0.69393253</td><td>0.099357804</td><td>4.75E-01</td><td>0.850111098</td></tr>
 <tr><td>14</td><td>S</td><td>2</td><td>0.381078283</td><td>0.088733361</td><td>2.28E-01</td><td>0.562769337</td></tr>
 <tr><td>15</td><td>f</td><td>0</td><td>0.009414082</td><td>0.001031668</td><td>7.59E-03</td><td>0.011666983</td></tr>
 <tr><td>15</td><td>f</td><td>1</td><td>0.016046862</td><td>0.001849818</td><td>1.28E-02</td><td>0.020105742</td></tr>
 <tr><td>15</td><td>f</td><td>2</td><td>0.013761645</td><td>0.003365154</td><td>8.51E-03</td><td>0.022181978</td></tr>
 <tr><td>15</td><td>S</td><td>0</td><td>0.543785509</td><td>0.074491876</td><td>3.98E-01</td><td>0.682247857</td></tr>
 <tr><td>15</td><td>S</td><td>1</td><td>0.535893377</td><td>0.078994056</td><td>3.83E-01</td><td>0.682729142</td></tr>
 <tr><td>15</td><td>S</td><td>2</td><td>0.570942397</td><td>0.120038274</td><td>3.37E-01</td><td>0.776629575</td></tr>
 <tr><td>16</td><td>f</td><td>0</td><td>0.01136957</td><td>0.001183901</td><td>9.27E-03</td><td>0.0139402</td></tr>
 <tr><td>16</td><td>f</td><td>1</td><td>0.015004393</td><td>0.001719241</td><td>1.20E-02</td><td>0.01877471</td></tr>
 <tr><td>16</td><td>f</td><td>2</td><td>0.013192553</td><td>0.002892707</td><td>8.57E-03</td><td>0.020246653</td></tr>
 <tr><td>16</td><td>S</td><td>0</td><td>0.64157852</td><td>0.083667394</td><td>4.67E-01</td><td>0.785051621</td></tr>
 <tr><td>16</td><td>S</td><td>1</td><td>0.537779336</td><td>0.077228942</td><td>3.88E-01</td><td>0.681431323</td></tr>
 <tr><td>16</td><td>S</td><td>2</td><td>0.467818642</td><td>0.104360074</td><td>2.79E-01</td><td>0.666563469</td></tr>
 <tr><td>17</td><td>f</td><td>0</td><td>0.011698225</td><td>0.001164555</td><td>9.62E-03</td><td>0.014215233</td></tr>
 <tr><td>17</td><td>f</td><td>1</td><td>0.011017616</td><td>0.001403334</td><td>8.58E-03</td><td>0.014136594</td></tr>
 <tr><td>17</td><td>f</td><td>2</td><td>0.015648404</td><td>0.002887848</td><td>1.09E-02</td><td>0.022441324</td></tr>
 <tr><td>17</td><td>S</td><td>0</td><td>0.775597088</td><td>0.101099907</td><td>5.25E-01</td><td>0.915189669</td></tr>
 <tr><td>17</td><td>S</td><td>1</td><td>0.608743166</td><td>0.08998203</td><td>4.26E-01</td><td>0.765399972</td></tr>
 <tr><td>17</td><td>S</td><td>2</td><td>0.466810593</td><td>0.117657909</td><td>2.57E-01</td><td>0.688596776</td></tr>
 <tr><td>18</td><td>f</td><td>0</td><td>0.010227068</td><td>0.001051046</td><td>8.36E-03</td><td>0.012506454</td></tr>
 <tr><td>18</td><td>f</td><td>1</td><td>0.011441753</td><td>0.001444919</td><td>8.93E-03</td><td>0.014649439</td></tr>
 <tr><td>18</td><td>f</td><td>2</td><td>0.017490946</td><td>0.00291762</td><td>1.26E-02</td><td>0.02422934</td></tr>
 <tr><td>18</td><td>S</td><td>0</td><td>0.954979194</td><td>0.122689665</td><td>7.32E-02</td><td>0.999824504</td></tr>
 <tr><td>18</td><td>S</td><td>1</td><td>0.842087719</td><td>0.151787345</td><td>3.63E-01</td><td>0.980374684</td></tr>
 <tr><td>18</td><td>S</td><td>2</td><td>0.387592498</td><td>0.119558818</td><td>1.91E-01</td><td>0.629435184</td></tr>
 <tr><td>19</td><td>f</td><td>0</td><td>0.006738158</td><td>0.000851781</td><td>5.26E-03</td><td>0.00863071</td></tr>
 <tr><td>19</td><td>f</td><td>1</td><td>0.010787732</td><td>0.001396884</td><td>8.37E-03</td><td>0.013899098</td></tr>
 <tr><td>19</td><td>f</td><td>2</td><td>0.011069474</td><td>0.003843054</td><td>5.59E-03</td><td>0.02178833</td></tr>
 <tr><td>19</td><td>S</td><td>0</td><td>0.549028619</td><td>0.111941564</td><td>3.34E-01</td><td>0.747040312</td></tr>
 <tr><td>19</td><td>S</td><td>1</td><td>0.738888127</td><td>0.169222158</td><td>3.37E-01</td><td>0.940437818</td></tr>
 <tr><td>19</td><td>S</td><td>2</td><td>0.756759494</td><td>0.243292823</td><td>1.89E-01</td><td>0.97646712</td></tr>
 <tr><td>20</td><td>f</td><td>0</td><td>0.008870422</td><td>0.001377301</td><td>6.54E-03</td><td>0.012020218</td></tr>
 <tr><td>20</td><td>f</td><td>1</td><td>0.027759908</td><td>0.001987004</td><td>2.41E-02</td><td>0.031931466</td></tr>
 <tr><td>20</td><td>f</td><td>2</td><td>0.000130914</td><td>0.000467743</td><td>1.19E-07</td><td>0.125975224</td></tr>
 <tr><td>20</td><td>S</td><td>0</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
 <tr><td>20</td><td>S</td><td>1</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
 <tr><td>20</td><td>S</td><td>2</td><td>0.999200358</td><td>0.002922354</td><td>4.90E-01</td><td>0.999999384</td></tr>
</table>
</details>

### R code

The R code for the GLM is given below

<details>
<summary>R code</summary>
<pre><code>
library(readxl)
library(dplyr)
library(tidyr)

setwd(dirname(parent.frame(2)$ofile))

windowsFonts(Font = windowsFont("CMU Serif"))

#Read data
adults    <- read_xlsx("../data/blackbirds/adults.xlsx")
juveniles <- read_xlsx("../data/blackbirds/juveniles.xlsx")
pulliSep  <- read_xlsx("../data/blackbirds/pulli.xlsx")
pulliComb <- read_xlsx("../data/blackbirds/pulliComb.xlsx")

#Select recovery counts
R     <- adults    %>% select(-c("Year of ringing", "Total ringed"))
Q     <- juveniles %>% select(-c("Year of ringing", "Total ringed"))
PSep  <- pulliSep  %>% select(-c("Year of ringing", "Total ringed"))
PComb <- pulliComb %>% select(-c("Year of ringing", "Total ringed"))

#Select totals ringed
N <- adults    %>% select("Total ringed")
M <- juveniles %>% select("Total ringed")
L <- pulliSep  %>% select("Total ringed")

#Convert recovery counts to matrix
R     <- as.matrix(R)
Q     <- as.matrix(Q)
PSep  <- as.matrix(PSep)
PComb <- as.matrix(PComb)

#Convert totals ringed to vectors
N <- as.vector(N)$`Total ringed`
M <- as.vector(M)$`Total ringed`
L <- as.vector(L)$`Total ringed`

#Inverse logit function
ilogit <- function(x){
  1/(1+exp(-x))
}

#Likelihood function for separate post-fledging recoveries
likelihood3A_PFsep <- function(params,
                               R, P, Q,
                               N, M, L){
  l <- dim(R)[1]
  
  #Parameters in logit space
  logitf0 <- params[1:l]
  logitf1 <- params[(l+1):(2*l)]
  logitf2 <- params[(2*l+1):(3*l)]
  logitS0 <- params[(3*l+1):(4*l-1)]
  logitS1 <- params[(4*l):(5*l-2)]
  logitS2 <- params[(5*l-1):(6*l-2)]
  
  #Parameters bounded between 0 and 1
  f0 <- ilogit(logitf0)
  f1 <- ilogit(logitf1)
  f2 <- ilogit(logitf2)
  S0 <- ilogit(logitS0)
  S1 <- ilogit(logitS1)
  S2 <- ilogit(logitS2)
  
  #Initialise theta vectors
  theta0 <- numeric(l)
  theta1 <- numeric(l)
  theta2 <- numeric(l)
  
  #Initialise row likelihood vectors
  lik0 <- numeric(l)
  lik1 <- numeric(l)
  lik2 <- numeric(l)
  
  #Create theta values
  for (i in l:1){
    if (i == l){
      theta0[i] <- f0[i]
      theta1[i] <- f1[i]
      theta2[i] <- f2[i]
    } else {
      theta0[i] <- f0[i] + S0[i]*theta0[i+1]
      theta1[i] <- f1[i] + S1[i]*theta0[i+1]
      theta2[i] <- f2[i] + S2[i]*theta1[i]
    }
  }
  
  eps <- 1e-12
  
  #Ensure theta values are valid
  theta0 <- pmin(pmax(theta0, eps), 1 - eps)
  theta1 <- pmin(pmax(theta1, eps), 1 - eps)
  theta2 <- pmin(pmax(theta2, eps), 1 - eps)
  
  ##Adult likelihood
  #Loop over rows
  for (p in 1:l){
    likAdult <- 0
    
    #Loop over columns
    for (i in p:l){
      
      #Main diagonal
      if (i == p){
        probAdult <- f0[i]
      } 
      
      #All others
      else {
        probAdult <- prod(S0[p:(i-1)]) * f0[i]
      }
      
      #Add value to likelihood according to log likelihood function
      likAdult <- likAdult + R[p, i]*log(probAdult)
    }
    
    #Add probability of non-recovery and assign to row likelihood
    lik0[p] <- likAdult + (N[p] - rowSums(R)[p]) * log(1 - theta0[p])
  }
  
  ##Juvenile likelihood
  #Loop over rows
  for (p in 1:l){
    likJuvenile <- 0
    
    #Loop over columns
    for (i in p:l){
      
      #Main diaongal
      if (i == p){
        probJuvenile <- f1[i]
      } 
      
      #One above main diaongal
      else if (i == p+1){
        probJuvenile <- S1[p]*f0[i]
      } 
      
      #All others
      else {
        probJuvenile <- S1[p] * prod(S0[(p+1):(i-1)]) * f0[i]
      }
      
      #Add value to likelihood according to log likelihood function
      likJuvenile <- likJuvenile + Q[p, i] * log(probJuvenile)
    }
    
    #Add probability of non-recovery and assign to row likelihood
    lik1[p] <- likJuvenile + (M[p] - rowSums(Q)[p]) * log(1 - theta1[p])
  }
  
  ##Pullus likelihood
  #Loop over rows
  for (p in 1:l){
    likPullus <- 0
    
    #Loop over columns
    for (i in p:(l+1)){
      
      #Main diagonal
      if (i == p){
        probPullus <- f2[i]
      } 
      
      #One above main diagonal
      else if (i == p+1){
        probPullus <- S2[p]*f1[p]
      } 
      
      #Two above main diagonal
      else if (i == p+2){
        probPullus <- S2[p]*S1[p]*f0[p+1]
      } 
      
      #All others
      else {
        probPullus <- S2[p] * S1[p] * prod(S0[(p+1):(i-2)]) * f0[i-1]
      }
      
      #Add value to likelihood according to log likelihood function
      likPullus <- likPullus + P[p, i] * log(probPullus)
    }
    
    #Add probability of non-recovery and assign to row likelihood
    lik2[p] <- likPullus + (L[p] - rowSums(P)[p]) * log(1 - theta2[p])
  }
  
  #Sum all row likelihoods
  totalLik0 <- sum(lik0)
  totalLik1 <- sum(lik1)
  totalLik2 <- sum(lik2)
  
  #Sum of age likelihoods
  totalLik <- totalLik0 + totalLik1 + totalLik2
  
  #Return negative as optim minimises
  -totalLik
}

likelihood3A_PFcomb <- function(params,
                                R, P, Q,
                                N, M, L){
  l <- dim(R)[1]
  
  #Parameters in logit space
  logitf0 <- params[1:l]
  logitf1 <- params[(l+1):(2*l)]
  logitf2 <- params[(2*l+1):(3*l)]
  logitS0 <- params[(3*l+1):(4*l-1)]
  logitS1 <- params[(4*l):(5*l-2)]
  logitS2 <- params[(5*l-1):(6*l-2)]
  
  #Parameters bounded between 0 and 1
  f0 <- ilogit(logitf0)
  f1 <- ilogit(logitf1)
  f2 <- ilogit(logitf2)
  S0 <- ilogit(logitS0)
  S1 <- ilogit(logitS1)
  S2 <- ilogit(logitS2)
  
  #Initialise theta vectors
  theta0 <- numeric(l)
  theta1 <- numeric(l)
  theta2 <- numeric(l)
  
  #Initialise row likelihood vectors
  lik0 <- numeric(l)
  lik1 <- numeric(l)
  lik2 <- numeric(l)
  
  #Create theta values
  for (i in l:1){
    if (i == l){
      theta0[i] <- f0[i]
      theta1[i] <- f1[i]
      theta2[i] <- f2[i]
    } else {
      theta0[i] <- f0[i] + S0[i]*theta0[i+1]
      theta1[i] <- f1[i] + S1[i]*theta0[i+1]
      theta2[i] <- f2[i] + S2[i]*theta1[i]
    }
  }
  
  eps <- 1e-12
  
  #Ensure theta values are valid
  theta0 <- pmin(pmax(theta0, eps), 1 - eps)
  theta1 <- pmin(pmax(theta1, eps), 1 - eps)
  theta2 <- pmin(pmax(theta2, eps), 1 - eps)
  
  ##Adult likelihood
  #Loop over rows
  for (p in 1:l){
    likAdult <- 0
    
    #Loop over
    for (i in p:l){
      if (i == p){
        probAdult <- f0[i]
      } else {
        probAdult <- prod(S0[p:(i-1)]) * f0[i]
      }
      
      likAdult <- likAdult + R[p, i]*log(probAdult)
    }
    
    lik0[p] <- likAdult + (N[p] - rowSums(R)[p]) * log(1 - theta0[p])
  }
  
  #Juvenile likelihood
  for (p in 1:l){ #Rows (p moves down)
    likJuvenile <- 0
    
    for (i in p:l){ #Cols (i moves left to right)
      if (i == p){
        probJuvenile <- f1[i]
      } else if (i == p+1){
        probJuvenile <- S1[p]*f0[i]
      } else {
        probJuvenile <- S1[p] * prod(S0[(p+1):(i-1)]) * f0[i]
      }
      
      likJuvenile <- likJuvenile + Q[p, i] * log(probJuvenile)
    }
    
    lik1[p] <- likJuvenile + (M[p] - rowSums(Q)[p]) * log(1 - theta1[p])
  }
  
  #Pullus likelihood
  for (p in 1:l){
    likPullus <- 0
    
    for (i in p:l){
      if (i == p){
        probPullus <- f2[i] + S2[i]*f1[i]
      } else if (i == p+1){
        probPullus <- S2[p]*S1[p]*f0[p+1]
      } else {
        probPullus <- S2[p] * S1[p] * prod(S0[(p+1):(i-1)]) * f0[i]
      }
      
      likPullus <- likPullus + P[p, i] * log(probPullus)
    }
    
    lik2[p] <- likPullus + (L[p] - rowSums(P)[p]) * log(1 - theta2[p])
  }
  
  totalLik0 <- sum(lik0)
  totalLik1 <- sum(lik1)
  totalLik2 <- sum(lik2)
  
  totalLik <- totalLik0 + totalLik1 + totalLik2
  
  -totalLik
}

nPar <- 118

inits <- rnorm(nPar)

resultSep <- optim(inits, likelihood3A_PFsep,
                   R = R, Q = Q, P = PSep, N = N, M = M, L = L,
                   method = "L-BFGS-B", hessian = TRUE)

resultComb <- optim(inits, likelihood3A_PFcomb,
                    R = R, Q = Q, P = PComb, N = N, M = M, L = L,
                    method = "L-BFGS-B", hessian = TRUE)

l <- dim(R)[1]

pprint <- function(x){
  cat(x, sep = "\n")
}

#Function to pad vector x to length n
pad <- function(x, n) {
  length(x) <- n
  x
}

#Function to build dataframe of estimates, standard errors, and confidence interval bounds given optim result
getEstimsDf <- function(result){
  #Get estimates
  params <- result$par
  
  #Extract values
  logitf0 <- params[1:l]
  logitf1 <- params[(l+1):(2*l)]
  logitf2 <- params[(2*l+1):(3*l)]
  logitS0 <- params[(3*l+1):(4*l-1)]
  logitS1 <- params[(4*l):(5*l-2)]
  logitS2 <- params[(5*l-1):(6*l-2)]
  
  #Bound to (0, 1)
  f0 <- ilogit(logitf0)
  f1 <- ilogit(logitf1)
  f2 <- ilogit(logitf2)
  S0 <- ilogit(logitS0)
  S1 <- ilogit(logitS1)
  S2 <- ilogit(logitS2)
  
  #Calculate standard errors in logit space
  logitHess    <- result$hessian
  logitCov <- solve(logitHess)
  logitStdErrs <- sqrt(diag(logitCov))
  
  #Calculate 95% confidence interval bounds in logit space
  logitLower <- params - 1.96 * logitStdErrs
  logitUpper <- params + 1.96 * logitStdErrs
  
  #Bound 95% confidence interval bounds in bounded space
  lower <- ilogit(logitLower)
  upper <- ilogit(logitUpper)
  
  ilogitParams <- ilogit(params)
  
  #Calculate standard errors using delta method
  stdErrs <- logitStdErrs * ilogitParams * (1 - ilogitParams)
  
  #Extract standard errors
  f0StdErr <- stdErrs[1:l]
  f1StdErr <- stdErrs[(l+1):(2*l)]
  f2StdErr <- stdErrs[(2*l+1):(3*l)]
  S0StdErr <- stdErrs[(3*l+1):(4*l-1)]
  S1StdErr <- stdErrs[(4*l):(5*l-2)]
  S2StdErr <- stdErrs[(5*l-1):(6*l-2)]
  
  #Extract lower bounds
  f0Lower <- lower[1:l]
  f1Lower <- lower[(l+1):(2*l)]
  f2Lower <- lower[(2*l+1):(3*l)]
  S0Lower <- lower[(3*l+1):(4*l-1)]
  S1Lower <- lower[(4*l):(5*l-2)]
  S2Lower <- lower[(5*l-1):(6*l-2)]
  
  #Extract upper bounds
  f0Upper <- upper[1:l]
  f1Upper <- upper[(l+1):(2*l)]
  f2Upper <- upper[(2*l+1):(3*l)]
  S0Upper <- upper[(3*l+1):(4*l-1)]
  S1Upper <- upper[(4*l):(5*l-2)]
  S2Upper <- upper[(5*l-1):(6*l-2)]
  
  #Pad S0, S1 values to l (there are l-1 S0, S1 estiamtes)
  S0 <- pad(S0, l)
  S1 <- pad(S1, l)
  
  S0StdErr <- pad(S0StdErr, l)
  S1StdErr <- pad(S1StdErr, l)
  
  S0Lower <- pad(S0Lower, l)
  S1Lower <- pad(S1Lower, l)
  
  S0Upper <- pad(S0Upper, l)
  S1Upper <- pad(S1Upper, l)
  
  #Create dataframe
  GLMEstimsDf <- data.frame(time = 1:l,
                            f0Estim = f0, f1Estim = f1, f2Estim = f2,
                            S0Estim = S0, S1Estim = S1, S2Estim = S2,
                            f0StdErr, f1StdErr, f2StdErr,
                            S0StdErr, S1StdErr, S2StdErr,
                            f0Lower, f1Lower, f2Lower,
                            S0Lower, S1Lower, S2Lower,
                            f0Upper, f1Upper, f2Upper,
                            S0Upper, S1Upper, S2Upper)
  
  #Format dataframe
  df <- GLMEstimsDf %>%
    pivot_longer(-time,
                 names_to = c("Param", "Age", "Type"),
                 names_pattern = "([A-Za-z])([0-9])([A-Za-z]+)") %>%
    pivot_wider(names_from = Type,
                values_from = value)
  
  df
}

#Create dataframe from optim results
dfSep  <- getEstimsDf(resultSep) %>%
  mutate(Model = "Sep")
dfComb <- getEstimsDf(resultComb) %>%
  mutate(Model = "Comb")

#Combine dataframes
df <- rbind(dfSep, dfComb)
</code></pre>
</details>

<style>
  body{
    margin-bottom: 100px;
  }
</style>