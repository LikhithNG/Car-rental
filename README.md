# Car Rental Portal

The customer-facing pages of a PHP car rental website. Visitors browse and
search vehicles. Logged-in users book a vehicle for a date range, track their
bookings, post testimonials and manage their profile. These pages query the database
through PDO prepared statements.

> **Status: incomplete repository.** The shared includes, static assets, admin
> panel and database schema are not committed, so the site **cannot run from a
> fresh clone**. See [What is missing](#what-is-missing-to-run-it).

## Pages

| File | Purpose | Login required |
|------|---------|----------------|
| `index.php` | Home page | No |
| `car-listing.php` | List all vehicles | No |
| `search-carresult.php` | Vehicle search results | No |
| `vehical-details.php` | Vehicle details and booking form (`?vhid=<id>`) | Only to submit a booking |
| `page.php` | Static content pages from `tblpages` (`?type=<type>`) | No |
| `contact-us.php` | Contact form and contact info | No |
| `check_availability.php` | AJAX endpoint that checks whether a registration email is already taken | No |
| `my-booking.php` | The current user's bookings and their status | Yes |
| `post-testimonial.php` | Submit a testimonial | Yes |
| `my-testimonials.php` | The current user's testimonials | Yes |
| `profile.php` | View and update profile | Yes |
| `update-password.php` | Change password | Yes |
| `logout.php` | Destroy the session and redirect to the home page | No |

Login state is `$_SESSION['login']`, which holds the user's email. Protected
pages redirect to `index.php` when it is empty.

## What is missing to run it

The pages reference the following, none of which is in this repository.

<!-- TODO(author): commit these, or document where they come from. -->

- **`includes/`**: `config.php` (creates the PDO `$dbh` connection),
  `header.php`, `footer.php`, `sidebar.php`, `colorswitcher.php`, `login.php`,
  `registration.php` and `forgotpassword.php`. The login, registration and
  forgot-password handlers live in these files.
- **`assets/`**: CSS (Bootstrap, Owl Carousel, Slick, Font Awesome, colour
  switcher), JavaScript and images.
- **`admin/`**: the admin panel, including uploaded vehicle images under
  `admin/img/vehicleimages/`.
- **Database schema / seed SQL** for these tables: `tblusers`, `tblvehicles`,
  `tblbrands`, `tblbooking`, `tbltestimonial`, `tblpages`, `tblcontactusinfo`
  and `tblcontactusquery`.

Once those exist, serve the folder with PHP 7+ (the booking handler uses `??`)
and a database that matches the schema. Put the connection settings in
`includes/config.php`, which is git-ignored.

## Security notes

Recent fixes:

- Protected pages now `exit` right after the login redirect, so their content
  is no longer rendered to logged-out users.
- A booking requires a logged-in user. `vhid` must be a positive integer, and
  both dates must be valid `dd/mm/yyyy` values with To Date on or after From
  Date.

Known limitations (not yet fixed):

- **Passwords are hashed with unsalted MD5** (see `update-password.php`; login
  and registration live in the missing `includes/`). MD5 is not safe for
  passwords. Migrate to `password_hash()` / `password_verify()`, changing login,
  registration, forgot-password and update-password together. One way to do
  this: on a successful MD5 login, re-hash the password with `password_hash()`
  and store the new hash. The migration was not done here because the login and
  registration code is not in this repository, and changing only one side would
  break existing accounts.
- `error_reporting(0)` is set on most pages, which hides errors during
  development.
- The forms have no CSRF protection.
