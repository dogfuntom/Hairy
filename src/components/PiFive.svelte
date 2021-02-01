<script>
  import _p5 from './PiFiveAction';
  export let sketch = null;
  export let engine = null;

  let holder = null;

  export function restart() {
    engine.remove();
    engine = _p5(holder, sketch);
  }

  function foo(node, workspace) {
    holder = node;
    engine = _p5(node, workspace);
    return engine;
  }
</script>

<figure use:foo={sketch} />

<style>
  figure {
    display: inline;
    margin: 0;
  }
</style>