<script>
  import _p5 from './PiFiveAction';
  export let sketch = null;
  export let engine = null;

  function foo(node, workspace) {
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