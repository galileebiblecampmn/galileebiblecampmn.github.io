---
title: 'Rentals'
layout: page
order: 2
---

<div class="uk-grid">
<div class="uk-width-2-3@m uk-width-1-1">
    <table class="uk-table uk-table-divider uk-table-small uk-table-responsive">
        <thead>
            <tr>
                <th>Rental</th>
                <th>Rate</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>Campsites with electricty only (20 sites)</td>
                <td>$15/day</td>
            </tr>
            <tr>
                <td>Campsites with electricty, water, and sewer (10 sites)</td>
                <td>Call for availability</td>
            </tr>
            <tr>
                <td>Camper hookups</td>
                <td>$30/day</td>
            </tr>
            <tr>
                <td>Boys and Girls Dorms</td>
                <td>$15 for District Campers<br>$20 for Non-District Campers</td>
            </tr>
            <tr>
                <td>Staff Cabin</td>
                <td>$150/day (Max. 10 people)</td>
            </tr>
            <tr>
                <td>Cook's Cabin</td>
                <td>$100/day (Max. 6 people)</td>
            </tr>
            <tr>
                <td>Speaker's Cabin</td>
                <td>$100/day (Max 6 people)</td>
            </tr>
        </tbody>
    </table>
</div>
    
<div class="uk-width-1-3@m uk-width-1-1" markdown="1">
### Contact for Camp Rent

Donald Christianson

218-782-2706 or 218-689-7438 cell

P.O. Box 3 Greenbush, MN 56726

</div>
</div>

### Dates already taken for 2024

<table class="uk-table uk-table-small uk-table-responsive">
    <thead>
        <tr>
            <th>Date</th>
            <th>Event</th>
        </tr>
    </thead>
    <tbody>
    {% for date in site.data.dates %}
        <tr>
            <td class="uk-width-small uk-text-nowrap">{{ date.Date }}</td>
            <td>{{ date.Event }}</td>
        </tr>
    {% endfor %}
    </tbody>
</table>
