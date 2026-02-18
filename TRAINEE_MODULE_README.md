# Trainee User Interface Module

## Overview

The Trainee UI Module is a complete, production-ready interface for trainee users in the Laravel 12 + Vue 3 + Inertia.js application. It provides trainees with tools to manage their attendance, time tracking, view payslips, and access comprehensive metrics through an intuitive dashboard.

## Project Structure

```
app/Http/Controllers/trainee/
├── TraineeDashboardController.php          # Dashboard metrics and statistics
├── TraineeTimeKeepingController.php        # Time tracking (clock in/out)
├── TraineeAttendanceController.php         # Attendance records and calendar
└── TraineePayslipController.php            # Payslip management

resources/js/Pages/Trainee/
├── Dashboard.vue                           # Dashboard page component
├── TimeKeeping.vue                         # Time keeping page component
├── Attendance.vue                          # Attendance calendar page
└── Payslip.vue                             # Payslip list and details

resources/js/types/
└── trainee.ts                              # TypeScript interfaces and types

routes/
└── web.php                                 # Centralized trainee routes
```

## Features

### 1. Dashboard (`/trainee/dashboard`)

**Metrics Displayed:**
- Attendance percentage (last 30 days)
- Current payroll status with gross/net pay
- Leave balance (used/remaining/total)
- Attendance statistics (present, late, absent, on leave)
- Upcoming holidays (next 30 days)
- Recent attendance records (last 7 days)

**Components:**
- Attendance progress bar with color coding
- Quick action buttons
- Stat cards for attendance breakdown
- Recent attendance list with status badges
- Holiday calendar
- Payroll status summary

### 2. Time Keeping (`/trainee/timekeeping`)

**Features:**
- Real-time digital clock display
- Large Clock In/Clock Out buttons
- Status indicator (Not Clocked In / Clocked In / Clocked Out)
- Today's attendance record summary
- Weekly time records table
- Error prevention (no double clock-ins)
- Toast notifications for user feedback

**Data Displayed:**
- Current time and date
- Clock in/out times
- Work duration calculation
- Daily status

### 3. Attendance (`/trainee/attendance`)

**Features:**
- Interactive monthly calendar
- Color-coded days (Present/Late/Absent/On Leave)
- Month navigation (previous/next)
- Attendance statistics (monthly breakdown)
- Attendance rate calculation
- Detailed records table
- Export-ready data structure

**Statistics:**
- Total work days
- Present days
- Late days
- Absent days
- On leave days
- Attendance percentage

### 4. Payslip (`/trainee/payslip`)

**Features:**
- Payslip history list
- Detailed payslip breakdown
- Earnings section (base salary, overtime, night differential, etc.)
- Deductions section (SSS, PhilHealth, PAG-IBIG, taxes, loans)
- Gross and net pay calculation
- Status tracking
- PDF download functionality (mockup)

**Calculations:**
- Base salary and daily rates
- Night differential
- Overtime pay
- Sunday/Special holiday pay
- Late deductions
- Statutory deductions
- Net pay calculation

## Routes

All routes are protected with `auth`, `verified`, and `position:trainee` middleware.

```php
Route::prefix('trainee')->middleware(['auth', 'verified', 'position:trainee'])->group(function () {
    Route::get('/dashboard', [TraineeDashboardController::class, 'index'])->name('trainee.dashboard');
    Route::get('/timekeeping', [TraineeTimeKeepingController::class, 'index'])->name('trainee.timekeeping');
    Route::post('/timekeeping/clock', [TraineeTimeKeepingController::class, 'clockInOut'])->name('trainee.timekeeping.clock');
    Route::get('/attendance', [TraineeAttendanceController::class, 'index'])->name('trainee.attendance');
    Route::get('/payslip', [TraineePayslipController::class, 'index'])->name('trainee.payslip');
    Route::get('/payslip/{payroll}', [TraineePayslipController::class, 'show'])->name('trainee.payslip.show');
});
```

## Controllers

### TraineeDashboardController

**Methods:**
- `index()` - Fetch and render dashboard data

**Private Methods:**
- `calculateAttendancePercentage()` - Calculate attendance % for last 30 days
- `calculateLeaveBalance()` - Calculate used/remaining leave days
- `getAttendanceStatistics()` - Get breakdown of attendance status

**Data Returned:**
- User information
- Attendance percentage
- Current payroll status
- Upcoming holidays
- Recent attendance records
- Leave balance
- Attendance statistics

### TraineeTimeKeepingController

**Methods:**
- `index()` - Render time keeping page
- `clockInOut(Request $request)` - Handle clock in/out actions

**Private Methods:**
- `calculateDuration()` - Calculate time between clock in and out
- `determinCurrentStatus()` - Determine current attendance status

**Validation:**
- Prevents double clock-ins
- Requires clock-in before clock-out
- Validates user position is trainee

### TraineeAttendanceController

**Methods:**
- `index(Request $request)` - Render attendance page with calendar

**Query Parameters:**
- `month` - Month number (1-12)
- `year` - Year value

**Private Methods:**
- `buildCalendar()` - Build monthly calendar structure
- `calculateStatistics()` - Calculate monthly statistics
- `getStatusClass()` - Get Tailwind CSS class for status
- `getStatusBadge()` - Get status badge information
- `calculateDuration()` - Format time duration

### TraineePayslipController

**Methods:**
- `index(Request $request)` - Render payslip page
- `show(Payroll $payroll)` - Get specific payslip details (API)

**Private Methods:**
- `formatPayslipDetails()` - Convert database format to frontend format
- `calculateTotalDeductions()` - Sum all deductions
- `formatPayslipPeriod()` - Format payslip period string
- `getStatusBadge()` - Get status badge information

**Authorization:**
- Validates user is trainee
- Ensures users can only view their own payslips

## Database Models

### User
- `id`, `name`, `email`, `position`, `employee_id`, `role`, `department`, `join_date`, `is_active`

### AttendanceLog
- `user_id`, `date`, `clock_in`, `clock_out`, `status`
- Relationship: `belongsTo(User::class)`

### Payroll
- Comprehensive salary breakdown fields
- `employee_id`, `gross_pay`, `net_pay`, `status`
- Relationship: `belongsTo(User::class)`

### LeaveRequest
- `user_id`, `leave_type`, `start_date`, `end_date`, `reason`, `status`
- Relationship: `belongsTo(User::class)`

### Holiday
- `holiday_date`, `holiday_name`, `holiday_type`, `premium_rate`

## TypeScript Interfaces

Located in `resources/js/types/trainee.ts`, provides type-safe prop definitions:

- `User` - User information
- `AttendanceRecord` - Daily attendance record
- `Holiday` - Holiday information
- `Payroll` - Payroll data
- `LeaveBalance` - Leave balance tracking
- `AttendanceStatistics` - Attendance breakdown
- `DashboardProps` - Dashboard component props
- `DailyRecord` - Time keeping record
- `TimeKeepingProps` - Time keeping component props
- `CalendarDay` - Calendar day information
- `AttendanceStats` - Monthly statistics
- `DetailedAttendanceRecord` - Detailed attendance entry
- `AttendancePageProps` - Attendance page props
- `PayslipListItem` - Payslip list entry
- `PayslipDeductions` - Deduction breakdown
- `PayslipDetails` - Complete payslip details
- `PayslipPageProps` - Payslip page props

## Styling & Design

### Design Principles
- **Modern, Clean UI** - Uses Tailwind CSS 3.2 for styling
- **Responsive** - Mobile-first approach with `lg:` breakpoints
- **Accessible** - WCAG 2.1 AA compliant with proper color contrast
- **Consistent** - Shared color scheme and spacing patterns

### Color Scheme
- **Primary** - Indigo (600, 700)
- **Success** - Green (500, 600, 700)
- **Warning** - Yellow (500, 600, 700)
- **Danger** - Red (500, 600, 700)
- **Info** - Blue (500, 600, 700)

### Components Used
- **Heroicons** - SVG icons from @heroicons/vue/24/outline
- **Custom components** - AuthenticatedLayout, Inertia Link
- **shadcn-vue** - Ready for integration

## State Management

The module uses:
- **Vue 3 Composition API** - `ref`, `computed`, `onMounted`
- **Reactive state** - For UI interactions (modals, loading states)
- **Computed properties** - For derived state calculations
- **Inertia props** - For server-side data passing

## Error Handling

### Frontend Validation
- Button state management (enabled/disabled)
- Loading states during API calls
- Toast notifications for success/error feedback

### Backend Validation
- Authorization checks (position: trainee)
- Data access restrictions (users can only view their own data)
- Business logic validation (no double clock-ins)

## Security Features

1. **Authentication** - All routes protected with `auth` middleware
2. **Authorization** - Position-based access control (`position:trainee`)
3. **Data Privacy** - Users can only access their own records
4. **CSRF Protection** - Built into Inertia.js and Laravel
5. **Rate Limiting** - Can be added via throttle middleware

## Performance Considerations

1. **Efficient Queries**
   - Uses `where`, `orderBy`, `take` for selective data retrieval
   - Eager loading relationships where needed
   - Indexes on `user_id`, `date`, `employee_id`

2. **Frontend Optimization**
   - Lazy loading payslip details via API
   - Computed properties for derived calculations
   - Minimal re-renders with Vue 3 reactivity

3. **Caching Opportunities**
   - Holiday list (static per year)
   - User profile information
   - Payroll historical data

## Future Enhancements

1. **PDF Export** - Implement actual PDF payslip download
2. **Email Notifications** - Notify trainees of status updates
3. **Mobile App** - Dedicated mobile app for time tracking
4. **Advanced Filtering** - Department, date range filters
5. **Bulk Operations** - Export attendance/payslip data
6. **Analytics** - Charts and graphs for performance trends
7. **Localization** - Multi-language support
8. **Dark Mode** - Dark theme support

## Troubleshooting

### Routes Not Found
- Ensure controllers are imported in `routes/web.php`
- Check that the namespace matches file location
- Verify middleware is correctly applied

### Props Not Displaying
- Check TypeScript interfaces match controller data
- Verify Inertia::render() is passing correct data keys
- Inspect browser console for prop-related warnings

### Time Zone Issues
- Ensure `config/app.php` has correct timezone
- Use Carbon for all date/time operations
- Convert to user timezone if needed

### Styling Not Applying
- Run `npm run dev` to rebuild Vite assets
- Clear browser cache
- Check Tailwind CSS is properly configured

## Contributing Guidelines

1. Follow Laravel PSR-12 coding standards
2. Use TypeScript for Vue components
3. Keep components under 300 lines
4. Write reusable helper functions
5. Document complex logic with comments
6. Test authorization on all routes
7. Validate all user inputs

## Support

For issues or questions:
1. Check TypeScript types for expected data format
2. Review controller methods for business logic
3. Inspect Inertia props in Vue DevTools
4. Check Laravel logs in `storage/logs/`
5. Verify middleware and route configuration
