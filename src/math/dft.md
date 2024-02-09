# 离散傅立叶变换
``` rust
///复数结构体
#[derive(Clone)]
struct Complex<T> {
	real: T,
	imag: T,
}

///复数加法
impl<T> Add for Complex<T>
where
    T: Add<Output = T> + Copy
{
    type Output = Complex<T>;

    fn add(self, other: Complex<T>) -> Complex<T> {
        Complex {
            real: self.real + other.real,
            imag: self.imag + other.imag,
        }
    }
}
///复数乘法
impl<T> Mul for Complex<T>
where
    T: Mul<Output = T> + Add<Output = T> + Copy + std::ops::Sub<Output = T>
{
    type Output = Complex<T>;

    fn mul(self, other: Complex<T>) -> Complex<T> {
        let real = self.real * other.real - self.imag * other.imag;
        let imag = self.real * other.imag + self.imag * other.real;
        Complex { real, imag }
    }
}
///离散傅立叶变换
use std::ops::{Add, Sub, Mul};
pub fn DFT<T: Copy + Default + std::convert::From<f64>>(signal: &[T]) -> Vec<Complex<T>>
where
    T: Add<Output = T> + Sub<Output = T> + Mul<Output = T> + Copy + Into<f64>
{
    let n = signal.len();
    let mut spectrum = vec![Complex{real: T::default(), imag: T::default()}; n];

    for k in 0..n {
        let mut sum = Complex{real: T::default(), imag: T::default()};
        for t in 0..n {
            let angle = -2.0 * std::f64::consts::PI * k as f64 * t as f64 / n as f64;
            let c = Complex{real: angle.cos().into(), imag: angle.sin().into()};
            let value = Complex{real: signal[t], imag: T::default()};
            sum = sum + value * c;
        }
        spectrum[k] = sum;
    }

    spectrum
}



```
