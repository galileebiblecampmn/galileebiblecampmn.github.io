---
title: 'Rentals'
layout: page
order: 2
fullcalendar: true
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

### Dates already taken for 2026

<div class="uk-card uk-card-default uk-card-body uk-margin">
  <p class="uk-margin-small">
    The calendar below shows booked dates between April and September 2026. Hover or tap a date to see event details.
    Dates not shaded are available.
  </p>

  <div class="rental-availability-legend" aria-label="Calendar legend">
    <div class="legend-item">
      <span class="legend-swatch legend-booked" aria-hidden="true"></span>
      <span>Booked</span>
    </div>
    <div class="legend-item">
      <span class="legend-swatch legend-available" aria-hidden="true"></span>
      <span>Available</span>
    </div>
    <div class="legend-item">
      <span class="legend-swatch legend-today" aria-hidden="true"></span>
      <span>Today</span>
    </div>
  </div>

  <div class="rental-calendar-controls uk-grid-small uk-flex-middle uk-margin-small-top" uk-grid>
    <div class="uk-width-auto">
      <button type="button" class="uk-button uk-button-default uk-button-small" id="rental-calendar-prev" aria-controls="rental-availability-calendar">
        Previous
      </button>
    </div>
    <div class="uk-width-expand uk-text-center">
      <span id="rental-calendar-title" class="rental-calendar-title" aria-live="polite"></span>
    </div>
    <div class="uk-width-auto">
      <button type="button" class="uk-button uk-button-default uk-button-small" id="rental-calendar-next" aria-controls="rental-availability-calendar">
        Next
      </button>
    </div>
  </div>

  <div id="rental-availability-calendar" class="rental-availability-calendar" role="region" aria-label="Rental availability calendar"></div>
  <div id="rental-calendar-popover" class="rental-calendar-popover" role="dialog" aria-live="polite" aria-hidden="true"></div>

  <details class="uk-margin-top">
    <summary class="uk-text-bold">Dates already taken (list view)</summary>
    <div class="uk-overflow-auto uk-margin-small-top">
      <table class="uk-table uk-table-small uk-table-divider uk-table-responsive">
        <thead>
          <tr>
            <th>Date</th>
            <th>Event</th>
          </tr>
        </thead>
        <tbody>
          {% for booking in site.data.dates %}
          {% assign start_label = booking.start_date | date: "%B %-d, %Y" %}
          {% assign end_label = booking.end_date | date: "%B %-d, %Y" %}
          <tr>
            <td class="uk-text-nowrap">
              {% if booking.start_date == booking.end_date %}
                <time datetime="{{ booking.start_date }}">{{ start_label }}</time>
              {% else %}
                <time datetime="{{ booking.start_date }}">{{ start_label }}</time> - <time datetime="{{ booking.end_date }}">{{ end_label }}</time>
              {% endif %}
            </td>
            <td>{{ booking.title }}</td>
          </tr>
          {% endfor %}
        </tbody>
      </table>
    </div>
  </details>
</div>

<script src="https://cdn.jsdelivr.net/npm/fullcalendar@6.1.20/index.global.min.js"></script>
<script>
  document.addEventListener('DOMContentLoaded', function () {
    if (typeof FullCalendar === 'undefined') {
      return;
    }

    const calendarEl = document.getElementById('rental-availability-calendar');
    const popoverEl = document.getElementById('rental-calendar-popover');
    const prevButton = document.getElementById('rental-calendar-prev');
    const nextButton = document.getElementById('rental-calendar-next');
    const titleEl = document.getElementById('rental-calendar-title');
    if (!calendarEl) {
      return;
    }

    const rawBookings = {{ site.data.dates | jsonify }};
    const bookings = rawBookings.map((booking) => {
      const startDate = booking.start_date || booking.start || booking.startDate;
      const endDate = booking.end_date || booking.end || booking.endDate;
      const title = booking.title || booking.event || booking.Event || booking.Title;

      if (!startDate || !endDate || !title) {
        return null;
      }

      return {
        start_date: startDate,
        end_date: endDate,
        title: title
      };
    }).filter(Boolean);

    const dateFormatter = new Intl.DateTimeFormat('en-US', {
      month: 'long',
      day: 'numeric',
      year: 'numeric',
      timeZone: 'UTC'
    });

    function parseIsoDate(isoDate) {
      const parts = isoDate.split('-').map(Number);
      return new Date(Date.UTC(parts[0], parts[1] - 1, parts[2]));
    }

    function formatIsoDate(dateObj) {
      const year = dateObj.getUTCFullYear();
      const month = String(dateObj.getUTCMonth() + 1).padStart(2, '0');
      const day = String(dateObj.getUTCDate()).padStart(2, '0');
      return `${year}-${month}-${day}`;
    }

    function addDays(isoDate, days) {
      const dateObj = parseIsoDate(isoDate);
      dateObj.setUTCDate(dateObj.getUTCDate() + days);
      return formatIsoDate(dateObj);
    }

    function eachDate(startIso, endIso, callback) {
      let cursor = parseIsoDate(startIso);
      const end = parseIsoDate(endIso);
      while (cursor <= end) {
        callback(formatIsoDate(cursor));
        cursor.setUTCDate(cursor.getUTCDate() + 1);
      }
    }

    const bookingsByDate = {};
    bookings.forEach((booking) => {
      eachDate(booking.start_date, booking.end_date, (isoDate) => {
        if (!bookingsByDate[isoDate]) {
          bookingsByDate[isoDate] = [];
        }
        bookingsByDate[isoDate].push(booking.title);
      });
    });

    function buildAriaLabel(isoDate, titles) {
      const readableDate = dateFormatter.format(parseIsoDate(isoDate));
      if (!titles || !titles.length) {
        return `${readableDate}. Available.`;
      }
      return `${readableDate}. Booked: ${titles.join('; ')}.`;
    }

    function setPopoverContent(isoDate, titles) {
      if (!popoverEl) {
        return;
      }
      const readableDate = dateFormatter.format(parseIsoDate(isoDate));
      const items = titles.map((title) => `<li>${title}</li>`).join('');
      popoverEl.innerHTML = `
        <div class="rental-popover-date">${readableDate}</div>
        <div class="rental-popover-label">Booked:</div>
        <ul>${items}</ul>
      `;
    }

    function positionPopover(anchorEl) {
      if (!popoverEl) {
        return;
      }
      const anchorRect = anchorEl.getBoundingClientRect();
      const popoverRect = popoverEl.getBoundingClientRect();
      const viewportWidth = document.documentElement.clientWidth;
      const viewportHeight = document.documentElement.clientHeight;
      let left = anchorRect.left + window.scrollX + (anchorRect.width / 2) - (popoverRect.width / 2);
      let top = anchorRect.bottom + window.scrollY + 8;
      const minLeft = window.scrollX + 8;
      const maxLeft = window.scrollX + viewportWidth - popoverRect.width - 8;
      if (left < minLeft) {
        left = minLeft;
      }
      if (left > maxLeft) {
        left = maxLeft;
      }
      const maxTop = window.scrollY + viewportHeight - popoverRect.height - 8;
      if (top > maxTop) {
        top = anchorRect.top + window.scrollY - popoverRect.height - 8;
      }
      popoverEl.style.left = `${left}px`;
      popoverEl.style.top = `${top}px`;
    }

    let pinnedDate = null;

    function showPopover(anchorEl, isoDate, titles, pinned) {
      if (!popoverEl) {
        return;
      }
      setPopoverContent(isoDate, titles);
      popoverEl.classList.add('is-visible');
      popoverEl.setAttribute('aria-hidden', 'false');
      popoverEl.dataset.anchorDate = isoDate;
      if (pinned) {
        popoverEl.dataset.pinned = 'true';
        pinnedDate = isoDate;
      } else {
        popoverEl.removeAttribute('data-pinned');
        pinnedDate = null;
      }
      positionPopover(anchorEl);
    }

    function hidePopover(force) {
      if (!popoverEl) {
        return;
      }
      if (!force && popoverEl.dataset.pinned === 'true') {
        return;
      }
      popoverEl.classList.remove('is-visible');
      popoverEl.setAttribute('aria-hidden', 'true');
      popoverEl.removeAttribute('data-pinned');
      popoverEl.removeAttribute('data-anchor-date');
      pinnedDate = null;
    }

    document.addEventListener('click', (event) => {
      if (!popoverEl || !popoverEl.classList.contains('is-visible')) {
        return;
      }
      if (popoverEl.contains(event.target)) {
        return;
      }
      const dayCell = event.target.closest('.fc-daygrid-day');
      if (dayCell && dayCell.dataset.date === pinnedDate) {
        return;
      }
      hidePopover(true);
    });

    window.addEventListener('resize', () => {
      if (!popoverEl || !popoverEl.classList.contains('is-visible')) {
        return;
      }
      const anchorDate = popoverEl.dataset.anchorDate;
      if (!anchorDate) {
        return;
      }
      const anchorEl = calendarEl.querySelector(`.fc-daygrid-day[data-date="${anchorDate}"]`);
      if (anchorEl) {
        positionPopover(anchorEl);
      }
    });

    window.addEventListener('scroll', () => {
      if (popoverEl && popoverEl.classList.contains('is-visible')) {
        hidePopover(true);
      }
    });

    let calendar = null;

    function bindCalendarControl(buttonEl, action) {
      if (!buttonEl) {
        return;
      }
      let lastTouch = 0;
      buttonEl.addEventListener('touchstart', (event) => {
        lastTouch = Date.now();
        event.preventDefault();
        action();
      }, { passive: false });
      buttonEl.addEventListener('click', () => {
        if (Date.now() - lastTouch < 500) {
          return;
        }
        action();
      });
    }

    calendar = new FullCalendar.Calendar(calendarEl, {
      initialView: 'dayGridMonth',
      initialDate: '2026-04-01',
      headerToolbar: false,
      showNonCurrentDates: false,
      fixedWeekCount: false,
      height: 'auto',
      navLinks: false,
      editable: false,
      eventDisplay: 'background',
      validRange: {
        start: '2026-04-01',
        end: '2026-10-01'
      },
      events: bookings.map((booking) => ({
        title: booking.title,
        start: booking.start_date,
        end: addDays(booking.end_date, 1)
      })),
      datesSet: (info) => {
        if (titleEl) {
          titleEl.textContent = info.view.title;
        }
      },
      dayCellDidMount: (info) => {
        const isoDate = info.dateStr;
        const titles = bookingsByDate[isoDate] || [];
        info.el.dataset.date = isoDate;
        info.el.classList.add('rental-day-cell');
        info.el.setAttribute('tabindex', '0');
        info.el.setAttribute('aria-label', buildAriaLabel(isoDate, titles));

        if (titles.length) {
          info.el.classList.add('rental-day-booked');
          info.el.setAttribute('aria-haspopup', 'dialog');
          info.el.setAttribute('role', 'button');
        } else {
          info.el.classList.add('rental-day-available');
        }

        if (!titles.length || !popoverEl) {
          return;
        }

        info.el.addEventListener('mouseenter', () => {
          if (pinnedDate) {
            return;
          }
          showPopover(info.el, isoDate, titles, false);
        });
        info.el.addEventListener('mouseleave', () => hidePopover(false));
        info.el.addEventListener('focus', () => {
          if (pinnedDate) {
            return;
          }
          showPopover(info.el, isoDate, titles, false);
        });
        info.el.addEventListener('blur', () => hidePopover(false));
        info.el.addEventListener('keydown', (event) => {
          if (event.key === 'Enter' || event.key === ' ') {
            event.preventDefault();
            if (pinnedDate === isoDate) {
              hidePopover(true);
            } else {
              showPopover(info.el, isoDate, titles, true);
            }
          }
          if (event.key === 'Escape') {
            hidePopover(true);
          }
        });
        info.el.addEventListener('click', () => {
          if (pinnedDate === isoDate) {
            hidePopover(true);
            return;
          }
          showPopover(info.el, isoDate, titles, true);
        });
      }
    });

    calendar.render();

    bindCalendarControl(prevButton, () => calendar.prev());
    bindCalendarControl(nextButton, () => calendar.next());
  });
</script>
