(function(){
  let proxyEcharts = null;
  let originInit = null;
  const DefaultFontOptions = [
      { key: 'xAxis', stylePath: 'xAxis.axisLabel' },
      { key: 'yAxis', stylePath: 'yAxis.axisLabel' },
      { key: 'legend', stylePath: 'legend.textStyle' },
      { key: 'tooltip', stylePath: 'tooltip.textStyle' },
      { key: 'title', stylePath: 'title.textStyle' },
      { key: 'series', stylePath: 'series.n.label', isArray: true },
  ];
  function setVal(source, keys, val, setFn) {
      if (!Array.isArray(keys)) {
          keys = keys.split('.');
      }
      keys = [...keys];
      const valKey = keys.pop();
      while (keys.length) {
          const parentKey = keys.shift();
          if (typeof source[parentKey] !== 'object' && typeof source[parentKey] !== 'function') {
              const childKey = keys[0] === undefined ? valKey : keys[0];
              let newVal = {};
              if (!isNaN(childKey * 1)) {
                  newVal = [];
              }
              if (setFn) {
                  setFn(source, parentKey, newVal);
              } else {
                  source[parentKey] = newVal;
              }
          }
          source = source[parentKey];
      }
      if (setFn) {
          setFn(source, valKey, val);
      } else {
          source[valKey] = val;
      }
  }
  function setFontFamily(options) {
      return options;
  }
  function setSeriesFamily(options) {
      if (options.series) {
          options.series = options.series.map(item => {
              item.label = { ...item.label };
              return item;
          });
      }
      return options;
  }
  function addResizeListener(echartsInstance) {
      const mountDom = echartsInstance.getDom();
      const resizeObserver = new ResizeObserver(() => {
          echartsInstance.resize();
      });
      resizeObserver.observe(mountDom);
      return resizeObserver;
  }
  function proxyInit(...arg) {
      if (!originInit) return null;
      let instance = null;
      try {
        instance = originInit.apply(this, arg);
      } catch (e) {
        console.error(e);
        const div = document.createElement('div');
        div.style.display = 'none';
        document.body.appendChild(div);
        instance = originInit.call(this, div);
        document.body.removeChild(div);
      }
      const originSetOption = instance.setOption;
      addResizeListener(instance);
      instance.setOption = async function setOptionProxy(options) {
          options = options || {};
          options.animation = false;
          try {
            return originSetOption.call(this, options);
          } catch (e) {
            console.error(e);
            return originSetOption.call(this, {});
          }
      };
      return instance;
  }
  Object.defineProperty(globalThis, 'echarts', {
      get() {
          return proxyEcharts;
      },
      set(value) {
          originInit = value.init;
          defineInit(value);
          proxyEcharts = value;
      },
      configurable: true,
  });
  function defineInit(value) {
      Object.defineProperty(value, 'init', {
          get() {
              return proxyInit;
          },
          set(v) {
              originInit = v;
          },
          enumerable: true,
      });
  }
})()