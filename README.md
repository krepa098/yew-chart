# Yew Charting Library

`yew-chart` is a collection of components that can be assembled to form charts
for the [Yew framework](https://github.com/yewstack/yew).

Here is a soil moisture/rainfall chart that has been produced using this library:

<p align="center"><img src="./images/rainfall-moisture-chart.png " alt="Soil moisture/rainfall chart" width="70%" /></p>

Here's another chart produced by this library, representing what is known as ["Delta-T"](http://www.bom.gov.au/lam/deltat.shtml):

<p align="center"><img src="./images/delta-t.png" alt="Delta-T chart" width="70%" /></p>

By leveraging these SVG-based components many types of charts can be formed
with a great deal of flexibility. The library is intended as a toolkit that
provides conveniences for rendering chart primitives. These primitives can
be regarded at a similar level as SVG's primitives i.e. lines, polygons etc.

## Example Plots

Two basic projects are given in the `examples` folder.

### Line Chart

`examples/basic` is configured to output a basic line chart:

<p align="center"><img src="./images/basic-chart.png" alt="A line chart" width="70%" /></p>

...and here's the the Yew component that was used to generate it:

```rust
const WIDTH: f32 = 533.0;
const HEIGHT: f32 = 300.0;
const MARGIN: f32 = 50.0;
const TICK_LENGTH: f32 = 10.0;

struct App {
    data_set: Rc<SeriesData>,
    vertical_axis_scale: Rc<dyn AxisScale>,
    horizontal_axis_scale: Rc<dyn AxisScale>,
}

impl Component for App {
    type Message = ();

    type Properties = ();

    fn create(_ctx: &Context<Self>) -> Self {
        let end_date = Utc::now();
        let start_date = end_date.sub(Duration::days(4));
        let time = start_date..end_date;

        let circle_text_labeller = Rc::from(horizontal_series::circle_text_label("Label"));

        App {
            data_set: Rc::new(vec![
                (start_date.timestamp() as f32, 1.0, None),
                (
                    start_date.add(Duration::days(1)).timestamp() as f32,
                    4.0,
                    None,
                ),
                (
                    start_date.add(Duration::days(2)).timestamp() as f32,
                    3.0,
                    None,
                ),
                (
                    start_date.add(Duration::days(3)).timestamp() as f32,
                    2.0,
                    None,
                ),
                (
                    start_date.add(Duration::days(4)).timestamp() as f32,
                    5.0,
                    Some(circle_text_labeller),
                ),
            ]),
            horizontal_axis_scale: Rc::new(TimeAxisScale::new(time, Duration::days(1))),
            vertical_axis_scale: Rc::new(LinearAxisScale::new(0.0..5.0, 1.0)),
        }
    }

    fn update(&mut self, _ctx: &Context<Self>, _msg: Self::Message) -> bool {
        false
    }

    fn view(&self, _ctx: &Context<Self>) -> yew::Html {
        html! {
            <svg class="chart" viewBox={format!("0 0 {} {}", WIDTH, HEIGHT)} preserveAspectRatio="none">
                <HorizontalSeries
                    series_type={horizontal_series::SeriesType::Line}
                    name="some-series"
                    data={Rc::clone(&self.data_set)}
                    horizontal_scale={Rc::clone(&self.horizontal_axis_scale)}
                    horizontal_scale_step={Duration::days(2).num_seconds() as f32}
                    vertical_scale={Rc::clone(&self.vertical_axis_scale)}
                    x={MARGIN} y={MARGIN} width={WIDTH - (MARGIN * 2.0)} height={HEIGHT - (MARGIN * 2.0)} />

                <VerticalAxis
                    name="some-y-axis"
                    orientation={vertical_axis::Orientation::Left}
                    scale={Rc::clone(&self.vertical_axis_scale)}
                    x1={MARGIN} y1={MARGIN} y2={HEIGHT - MARGIN}
                    tick_len={TICK_LENGTH}
                    title={"Some Y thing".to_string()} />

                <HorizontalAxis
                    name="some-x-axis"
                    orientation={horizontal_axis::Orientation::Bottom}
                    scale={Rc::clone(&self.horizontal_axis_scale)}
                    x1={MARGIN} y1={HEIGHT - MARGIN} x2={WIDTH - MARGIN}
                    tick_len={TICK_LENGTH} />

            </svg>
        }
    }
}
```

### Bar Chart

Using the same Yew view method code as above, `series_type` within the `HorizontalSeries` tag can be edited to display a bar chart instead by using the `Bar` keys.

```rust
<HorizontalSeries series_type={horizontal_series::SeriesType::Bar} ... />
```

<p align="center"><img src="./images/bar-chart-30px.png" alt="A bar chart" width="70%" /></p>

### Scatter Plot

`examples/scatter` is configured to output a basic scatter plot. The method by which this is accomplished is slightly different to that of the `Line` and `Bar` charts. The labeller is relied upon for scatter plots.

## Contribution policy

Contributions via GitHub pull requests are gladly accepted from their original author. Along with any pull requests, please state that the contribution is your original work and that you license the work to the project under the project's open source license. Whether or not you state this explicitly, by submitting any copyrighted material via pull request, email, or other means you agree to license the material under the project's open source license and warrant that you have the legal authority to do so.

## License

This code is open source software licensed under the [Apache-2.0 license](./LICENSE).

© Copyright [Titan Class P/L](https://www.titanclass.com.au/), 2021
