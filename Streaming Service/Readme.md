The Problem: Sensor Anomaly Detection & Time-Series Analysis
This one steps into territory you haven't touched yet: time-series resampling, rolling windows, and detecting anomalies. It's the kind of analysis you'd do on IoT sensor data, server metrics, or anything that emits a measurement on a schedule.
You'll work with synthetic data from a fleet of industrial sensors. Two files:
sensors.csv — sensor_id, install_date, location, model, firmware_version
readings.csv — sensor_id, timestamp, temperature, pressure, vibration, status

Readings are roughly every 15 minutes, but with realistic gaps (sensors go offline, miss intervals, get rebooted)
status is one of ok, warning, error, offline
The data has anomalies baked in: sudden spikes, gradual drift, sensor failures, and correlated failures across nearby sensors

Tasks (easy → hard)
1. Data hygiene & gaps. Load both files. Parse timestamps. For each sensor, compute the longest gap between consecutive readings. Which sensors have suspicious gaps (> 1 hour)? What fraction of expected readings are missing per sensor, assuming a 15-minute cadence?
2. Resample to regular intervals. Sensor readings don't arrive on exact 15-minute boundaries. Resample each sensor's data to a clean 15-minute grid, taking the mean of any readings in each bucket and forward-filling small gaps (≤ 2 buckets) but leaving longer gaps as NaN. Hint: this is per-sensor, so groupby + resample, or a loop.
3. Rolling baseline & z-score anomalies. For each sensor, compute a 24-hour rolling mean and rolling standard deviation of temperature. Flag any reading where temperature is more than 3 standard deviations from the rolling mean as an anomaly. How many anomalies per sensor? Per location? Are anomalies clustered in time?
4. Sensor drift detection. Some sensors are slowly drifting out of calibration — their readings creep up or down over weeks even though the underlying physical reality is stable. Detect this by fitting a linear trend (slope) to each sensor's temperature over its full lifetime and flagging sensors whose slope exceeds a threshold (in degrees/day). Hint: numpy.polyfit or scipy.stats.linregress, applied per sensor.
5. Correlated failures. Sensors in the same location should generally move together (shared environment). For each location, compute the pairwise correlation matrix of sensor temperatures (after resampling). Find pairs of sensors that should be correlated (same location) but aren't (correlation < 0.5) — these are candidates for being broken. Conversely, find any cross-location pairs with surprisingly high correlation.
6. Stretch: cascading failure detection. The data contains at least one "cascading failure" — one sensor goes into error status, then within 2 hours, other sensors in the same location follow. Identify these events: find the first error in each location-time-cluster, then check whether other sensors in that location entered error status within the next 2 hours. Output a table of cascade events with the originating sensor, location, time, and the count of follow-on failures.
Skills this exercises
pd.to_datetime with timezone awareness, set_index on a DatetimeIndex, resample() and the difference between .mean() / .ffill() / .asfreq(), rolling() with time-based windows, shift() and diff() for gap detection, groupby().apply() with custom functions (the anti-pattern and when it's actually justified), pivot() for correlation matrices, and merging time-windowed data.
The interesting wrinkle: the answer to "how do I efficiently do X per sensor" is rarely "loop." It's usually groupby().transform() or groupby().resample() or, when those don't fit, a well-structured groupby().apply(). Knowing which to reach for is a real skill.